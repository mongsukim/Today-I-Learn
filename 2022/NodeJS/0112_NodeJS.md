# 서버를 띄우기 위한 기본 셋팅(express 라이브러리)

```javascript
const express = require('express');
const app = express();

app.listen();
```

```javascript
app.listen(서버 띄울 포트번호, 띄운 후 실행할 코드 );
```

```javascript
app.listen(8080, function(){
    console.log('listening on 8080')
} );
```

실행하기

```javascript
node server.js 
```

# Get 요청 처리하기
- 누군가가 /pet으로 방문을 하면 pet관련 안내문을 띄워주기

```javascript
app.get('/pet', function(req, res){
    res.send('펫쇼핑 사이트입니다.')
})
```

결과 : localhost:8080/pet 으로 접속하면 '펫쇼핑 사이트입니다' 보임


# 서버에서 HTML파일 전송 & Nodemon으로 자동화


## 서버 재실행 자동화시키기(nodemon설치)


```javascript
npm install -g nodemon

nodemon server.js

```

- 만약 빨간글씨로 오류가 난다면, 시작프로그램 > powershell 입력 후 관리자 권한으로 실행
- executionpolicy 입력시 Restricted 라고 출력되면 문제가 있는 것
- `set-executionpolicy unrestricted ` 입력
- Y 엔터


## HTML파일을 보내보자. 


- .sendFile(보낼파일경로)

```javascript
app.get('/', function(req, res){
    res.sendFile(__dirname + '/index.html')
})
```


# Bootstrap을 이용 

- bootstrap v5는 IE지원을 하지 않음
- bootstrap v4 는 IE11 + v3는 IE9 + 까지 지원


## bootstarp 검색 후 오른쪽에서 v4.6.x을 선택 후 Home에서 start를 클릭 후 Starter template 복사



- 복사 후 index.html에 적용
  
```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">

    <title>Hello, world!</title>
  </head>
  <body>
    <h1>Hello, world!</h1>

    <!-- Optional JavaScript; choose one of the two! -->

    <!-- Option 1: Bootstrap Bundle with Popper -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p" crossorigin="anonymous"></script>

    <!-- Option 2: Separate Popper and Bootstrap JS -->
    <!--
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.10.2/dist/umd/popper.min.js" integrity="sha384-7+zCNj/IqJ95wo16oMtfsKbZ9ccEh31eOz1HGyDuCQ6wgnyJNSYdrPa03rtR1zdB" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.min.js" integrity="sha384-QJHtvGhmr9XOIpI6YVutG+2QOK9T+ZnN4kzFN1RtK3zEFEIsxhlmWl5/YESvpZ13" crossorigin="anonymous"></script>
    -->
  </body>
</html>
```

## write.html 만들기


```javascript
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">
  <title>Document</title>
</head>
<body>
  
  <h4 class="container mt-4"><strong>글 작성페이지</strong></h4>

  <div class="container mt-3">
    <form action="/add" method="POST">
      <div class="form-group">
        <label>오늘의 할일</label>
        <input type="text" class="form-control" name="title">
      </div>
      <div class="form-group">
        <label>날짜</label>
        <input type="text" class="form-control" name="date">
      </div>
      <button type="submit" class="btn btn-outline-secondary">Submit</button>
    </form>
  </div>


  <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>
</body>
</html>

```



# 폼에 입력한 데이터를 서버에 전송(POST 요청)


server.js

```javascript
app.get('/write', function(req, res){
    res.sendFile(__dirname + '/write.html')
})

```

- 함수 안에 함수(function(){}) = 콜백함수
- 순차적으로 실행하고 싶을때 쓴다.

```javascript

.get('경로',function(요청내용, 응답할 방법){ } )

```

- es6문법 사용하기 
```javascript

app.get('/write, (req, res) => {
  res.sendFile()
  }

```

## submit 버튼 누르면 폼에 입력한 제목과 날짜를 서버로 전달하기 

- form에 속성 설정 (꼭 2개의 속성이 있어야 함. )

write.html
```javascript
    <form action="/add" method="POST">
```

server.js
```javascript
// 어떤 사람이 /add 경로로 post요청을 하면 ..을 해주자

app.post('/add', function(req,res){
    res.send('전송완료')
})
```

#### form에서 input에 적은 정보는 request(요청) 에 들어있다.
> 1. body parser 라이브러리 설치

`npm install body-parser`

- 설치 후 server.js에 입력

```
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({extended:true}));
```

> 2.form데이터의 경우 input에 name 쓰기

```javascript
        <input type="text" class="form-control" name="title">
...
        <input type="text" class="form-control" name="date">

```

> 3. req.body 라고 하면 요청했던 form에 적힌 데이터 수신 가능

server.js

```javascript
app.post('/add', function(req,res){
    res.send('전송완료')
    console.log(req.body.title)
    console.log(req.body)
})
```

>> 결과 : vscode 터미널에 form에 입력한 내용 찍힘

