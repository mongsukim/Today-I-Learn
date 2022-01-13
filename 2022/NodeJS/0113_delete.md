## Delete request with AJAX

### Delete 요청하는 법
1. method-override 라이브러리 이용 (Node에서 사용가능) 
2. Javascript AJAX 이용 

#### AJAX는 새로고침 없이 서버랑 통신할수 있게 도와주는 JS문법 

```js
<script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
      <script>
        $.ajax({
          method : 'DELETE',
          url :'요청할 경로'
          data:'요청과 함께 보낼 데이터'
        })
      </script>

```

server.js

```js
app.delete('/delete', function(req,res){
    console.log(req.body);
    req.body._id = parseInt(req.body._id); // parseInt는 문자열을 숫자로 변환해줌
    db.collection('post').deleteOne(req.body,function(error,result){
        console.log('삭제완료')
    })
})
```

list.ejs
- data-id에 글번호를 부여해서 삭제 요청하면 글번호로 인식하여 삭제하도록 함


```js
 <ul class="list-group">
        <% for (var i = 0; i < posts.length; i++ ){ %> 
        <li class="list-group-item">
          <p>글번호: <%= posts[i]._id %> </p>
            <h4>할일 제목 : <%= posts[i].제목 %></h4>
            <p>할일 마감날짜 : <%= posts[i].날짜 %></p>
            <button class="delete" data-id="<%= posts[i]._id %>">삭제</button>
        </li>
        <% } %>
      </ul>

      <script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
      <script>

        $('.delete').click(function(e){
          var postNum = e.target.dataset.id;
          $.ajax({
          method : 'DELETE',
          url :'/delete',
          data: {_id: postNum }
        }).done(function(result){

        })

        })
        
      </script>

```
