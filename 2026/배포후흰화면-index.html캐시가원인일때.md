# 배포만 하면 흰 화면이 뜬다 — CDN이 index.html을 1년 캐싱하고 있었다

> SPA를 CDN에 올려 서비스할 때 한 번은 밟게 되는 함정. 증상은 "배포 후 흰 화면"이고,
> 원인은 코드가 아니라 **캐시 정책**에 있다.

---

## 1. 증상

배포할 때마다 일부 사용자에게 흰 화면이 떴다.

- 서버는 정상, 빌드도 정상, 로컬에서는 재현되지 않음
- 강력 새로고침(Ctrl+Shift+R)을 하면 멀쩡해짐
- 시크릿 모드에서는 안 나타남
- 콘솔에는 이런 에러

```
Failed to load module script: The server responded with a non-JavaScript MIME type
GET /assets/main-a1b2c3d4.js  404 (Not Found)
```

"강력 새로고침하면 된다"는 말을 사용자에게 할 수는 없다. 그리고 재현이 안 되니 원인을 잡기도 어렵다.

---

## 2. 원인 — 해시 번들링과 장기 캐시가 만나면

### 번들러는 파일명에 해시를 붙인다

Vite, webpack 같은 번들러는 빌드 결과물에 콘텐츠 해시를 붙인다.

```
빌드 #1 →  /assets/main-a1b2c3d4.js
빌드 #2 →  /assets/main-e5f6g7h8.js   ← 내용이 바뀌면 파일명도 바뀐다
```

그리고 `index.html`이 그 파일명을 가리킨다.

```html
<!-- 빌드 #1의 index.html -->
<script type="module" src="/assets/main-a1b2c3d4.js"></script>
```

즉 **`index.html`은 매 배포마다 내용이 바뀌는 파일**이고, 배포가 끝나면 이전 해시의 청크는 사라진다.

### CDN 기본 설정이 이걸 망가뜨린다

CDN에 정적 사이트를 올릴 때, 흔히 배포 경로 전체에 장기 캐시를 건다. 성능상 자연스러운 선택처럼 보인다.

```
Cache-Control: public, max-age=31536000   ← 1년, 전체 경로에 일괄 적용
```

문제는 여기에 `index.html`도 포함된다는 점이다. 그러면 이런 일이 벌어진다.

```
[배포 #1]
  사용자 접속 → index.html 다운로드 → 브라우저/CDN이 1년 캐싱
              → main-a1b2c3d4.js 로드 ✅

[배포 #2]  (새 빌드 업로드, 이전 청크 삭제됨)
  사용자 접속 → index.html ❌ 캐시된 구버전 사용 (1년 남음)
              → main-a1b2c3d4.js 요청 → 404 → 흰 화면
```

**캐시된 HTML이 이미 존재하지 않는 JS를 가리키고 있는 상태.** 코드에는 아무 문제가 없다.

재현이 안 됐던 이유도 여기서 설명된다 — 캐시가 없는 사용자(시크릿 모드, 신규 방문자)는 정상이고, 이전 버전을 방문한 적 있는 사용자만 깨진다.

---

## 3. 핵심 — 두 파일은 캐시 전략이 정반대여야 한다

여기서 중요한 건 "캐시를 끄자"가 아니다. **파일 성격에 따라 정책을 나누는 것**이다.

| | `index.html` | `/assets/*.js`, `*.css` |
|---|---|---|
| 파일명 | 항상 동일 | **콘텐츠 해시 포함** |
| 내용 | 배포마다 변함 | 파일명이 같으면 내용도 영원히 같음 |
| 캐시해도 되나 | **절대 안 됨** | **1년 캐시해도 안전** |

해시 기반 파일명의 성질을 생각해보면 당연하다. `main-a1b2c3d4.js`는 **내용이 바뀌는 순간 이름도 바뀌므로**, 그 URL의 내용은 절대 변하지 않는다. 그래서 무기한 캐시해도 안전하고, 오히려 그게 이 방식의 목적이다.

반면 `index.html`은 이름이 고정된 채 내용만 바뀐다. 캐시하면 안 되는 전형적인 파일이다.

> **한 줄 요약**
> 진입점(HTML)은 캐시하지 않고, 해시가 붙은 자산은 영구 캐시한다.

---

## 4. 해결

AWS Amplify Hosting 기준으로는 레포 루트에 `customHttp.yml`을 두면 된다.

```yaml
customHeaders:
  # index.html — 캐시 금지 (배포 후 구버전 청크 로드 실패 방지)
  - pattern: '**/*.html'
    headers:
      - key: 'Cache-Control'
        value: 'no-cache, no-store, must-revalidate'
      - key: 'Pragma'
        value: 'no-cache'
      - key: 'Expires'
        value: '0'

  # JS/CSS 청크는 파일명에 해시가 포함되어 있으므로 장기 캐시 가능
  - pattern: '/assets/**'
    headers:
      - key: 'Cache-Control'
        value: 'public, max-age=31536000, immutable'
```

### 각 값의 의미

**`no-cache, no-store, must-revalidate`**

- `no-cache` — 캐시는 하되 **쓰기 전에 항상 서버에 확인**한다 (이름이 헷갈리는 대표적 헤더)
- `no-store` — 아예 저장하지 않는다
- `must-revalidate` — 만료된 캐시를 재검증 없이 쓰지 못하게 한다

`no-cache`만으로도 대부분 동작하지만, 중간 프록시나 일부 모바일 WebView가 이를 느슨하게 해석하는 경우가 있어 함께 지정했다.

**`Pragma` / `Expires`**

HTTP/1.0 시대 헤더다. 요즘 브라우저는 `Cache-Control`만 보지만, 구형 WebView나 중간 프록시 대응용으로 남겨둔다. 비용이 없으니 넣어두는 쪽.

**`immutable`**

"만료 전까지는 재검증조차 하지 마라"는 의미. 이게 없으면 브라우저가 새로고침 시 `If-None-Match`로 확인 요청(304)을 보내는데, 해시 파일명에는 그 확인이 불필요하다. 붙이면 그 왕복이 사라진다.

### 다른 환경이라면

- **CloudFront 직접 구성** — Behavior를 경로별로 분리하고, `/assets/*`와 기본(`*`)에 서로 다른 Cache Policy를 적용
- **S3 정적 호스팅** — 업로드 시 객체별로 `--cache-control` 지정 (`aws s3 sync` 두 번 나눠 실행)
- **Nginx** — `location` 블록을 확장자로 나눠 `add_header Cache-Control` 지정

원리는 전부 같다. **진입점과 해시 자산을 분리한다.**

---

## 5. 검증

배포 후 헤더를 직접 확인한다.

```bash
# index.html — 캐시되면 안 됨
curl -sI https://example.com/ | grep -i cache-control
# → cache-control: no-cache, no-store, must-revalidate

# 해시 자산 — 장기 캐시여야 함
curl -sI https://example.com/assets/main-a1b2c3d4.js | grep -i cache-control
# → cache-control: public, max-age=31536000, immutable
```

CDN은 정책 변경 전에 캐싱해둔 응답을 그대로 들고 있으므로, **설정을 바꾼 뒤에는 무효화(invalidation)를 한 번 돌려야** 기존 사용자에게도 적용된다.

그리고 실제 증상 재현 테스트:

1. 배포 #1 상태에서 페이지 접속
2. 배포 #2 실행
3. **강력 새로고침이 아닌 일반 새로고침**으로 재접속
4. 흰 화면 없이 새 버전이 뜨면 해결

---

## 6. 남는 문제 — 이미 열려 있는 탭

캐시 정책을 고쳐도 한 가지가 남는다. **사용자가 이미 앱을 열어둔 채로 새 배포가 나가는 경우**다.

이때는 HTML을 다시 받지 않으므로, 그 탭은 여전히 구버전 청크를 참조한다. 특히 라우트 단위 코드 스플리팅을 쓰면, 사용자가 아직 방문하지 않은 화면으로 이동하는 순간 그 청크를 요청했다가 404를 맞는다.

이건 캐시 헤더로는 풀 수 없고, 별도 대응이 필요하다.

- 동적 import 실패를 잡아 새로고침 유도
- 빌드 버전을 폴링해 새 버전이 감지되면 "업데이트가 있습니다" 안내
- 이전 빌드 산출물을 일정 기간 함께 보관해 404 자체를 막기

```js
// 동적 import 실패 처리 예시
const load = (fn) =>
  fn().catch((err) => {
    if (/Failed to fetch dynamically imported module/.test(err.message)) {
      window.location.reload();
      return;
    }
    throw err;
  });
```

무한 새로고침을 막으려면 `sessionStorage`에 1회 플래그를 두는 편이 안전하다.

---

## 정리

- 배포 후 흰 화면 + 청크 404 → **코드가 아니라 캐시 정책을 먼저 의심한다**
- CDN 전체에 일괄 캐시 정책을 거는 것이 가장 흔한 원인
- **진입점 HTML은 캐시 금지, 해시가 붙은 정적 자산은 `immutable` 장기 캐시**
- 정책을 바꾼 뒤에는 CDN 무효화를 한 번 돌린다
- 이미 열려 있는 탭 문제는 별개이며, 애플리케이션 레벨에서 따로 처리한다

<br>

<sub>실서비스 하이브리드 앱(Capacitor + React + Vite)의 반복되던 배포 후 흰 화면 이슈를 추적하며 정리한 내용입니다.</sub>
