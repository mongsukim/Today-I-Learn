## Details page / URL parameters

#### view폴더를 만들고 detail.ejs파일 생성 후 부트스트랩 card템플릿 넣기

- public폴더를 만들고 main.css 추가

- server.js에 `app.use('/public', express.static('public'));` 추가 (미들웨어)

- detail.ejs에
` <link rel="stylesheet" href="/public/main.css">` 추가 
