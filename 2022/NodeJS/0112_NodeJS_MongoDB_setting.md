## DB의 종류 

1. 관계형 (엑셀 같이 칸이 나누어짐) MySQL, MariaDB, Oracle, MS SQL Server
- SQL이라는 언어를 써야 함 

2. NoSQL
- object 자료형으로 입출력 가능 (Dynamo, Oracle NoSQL, MongoDB, Redis, Cassandra


# 무료 호스팅 받아쓰기 

강의의 mongodb 설정 후 server.js에 연결

`npm install mongodb`

server.js에 추가
```javascript

const MongoClient = require('mongodb').MongoClient;

MongoClient.connect('mongodb+srv://몽고db아이디:비밀번호@cluster0.3rmzg.mongodb.net/myFirstDatabase?retryWrites=true&w=majority', function(error, client){
    if (error) return console.log(error);
    app.listen('8080', function(){
      console.log('listening on 8080')
    });
  })

```

## Database에 자료 저장하기



![image](https://user-images.githubusercontent.com/29091608/149077953-0a261b54-bf83-4b3b-8508-7cc6bc09c1db.png)

컬렉션은 일종의 파일들임

>mongoDB에서 Cluster0 -> Collections 누르기
>todoapp , post로 설정

![image](https://user-images.githubusercontent.com/29091608/149078171-ab692f13-580d-4058-a284-1c2637ceb8b7.png)

server.js
```javascript
const MongoClient = require('mongodb').MongoClient;

var db;

MongoClient.connect('mongodb+srv://아이디:비밀번호@cluster0.3rmzg.mongodb.net/myFirstDatabase?retryWrites=true&w=majority', 

function(error, client){
    if (error) return console.log(error);

    db = client.db('todoapp')

    db.collection('post').insertOne({이름:'john', 나이: 20}, function(error,result){
        console.log('저장완료')
    });

    app.listen('8080', function(){
      console.log('listening on 8080')
    });
  })


```

위의 코드를 실행하면 아래처럼 몽고db에 저장됨
![image](https://user-images.githubusercontent.com/29091608/149078801-1a24ab86-bd41-461f-a28b-af84274976ed.png)


## /add 요청 보내면 db에 객체 저장하기

**insertOne** 사용

```javascript
app.post('/add', function(req,res){
        db.collection('post').insertOne({제목:req.body.title, 날짜:req.body.date }, function(error,result){
            console.log('저장완료')
        });

    })
```


## 서버 데이터 넣어 HTML 만드는 법 (EJS 사용)

### EJS

1. 터미널에서 ejs 설치. `npm install ejs`
2. server.js에 EJS관련 코드 작성
```
app.set('view engine', 'ejs');

```

3. list.html 파일을 list.ejs로 확장자 바꾸기

4. server.js
>list로 get요청으로 접속하면 db에 저장된 데이터들로 꾸며진 html을 보여주기
>ejs파일의 위치는 view폴더 내에 넣기

```javascript
    app.get('/list', function(req,res){
        res.render('list.ejs');
    })
```

### DB데이터 읽기

list.ejs파일에 반복문 적용

```javascript
 <% for (var i = 0; i < posts.length; i++ ){ %> 
        <h4>할일 제목 : <%= posts[i].제목 %></h4>
        <p>할일 마감날짜 : <%= posts[i].날짜 %></p>
      <% } %>
```



