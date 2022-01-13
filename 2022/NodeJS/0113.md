## 글 번호를 달아 db에 저장하기

- 몽고 db는 자동으로 글번호 부여하는 기능이 없다.
- counter라는 새 collection을 만들어 totalPost와 name을 주자. 여기서는 총 발행된 게시물의 갯수만 관리한다.


![image](https://user-images.githubusercontent.com/29091608/149244908-c124c90d-a6ee-41f0-9da4-1df4bac668ea.png)


server.js


```javascript
app.post('/add', function(req,res){
    res.send('전송완료');
    db.collection('counter').findOne({name : '게시물갯수'}, function(error, result){ //db의 총게시물 갯수 데이터를 가져온다
        console.log(result.totalPost)
        var totalPostnumber = result.totalPost;
        db.collection('post').insertOne({ __id: totalPostnumber+1, 제목:req.body.title, 날짜:req.body.date }, function(error,result){ 
        //가져온 후 _id:총게시물갯수+1해서 새 데이터를 post콜렉션에 저장
            console.log('저장완료')
        // counter라는 콜렉션에 있는 totalPost라는 항목도 1 증가시켜야 한다 (수정)
        
        });
    });
});
```

### counter내의 totalPost를 1 증가시키기

```javascript
app.post('/add', function(req,res){
    res.send('전송완료');
    db.collection('counter').findOne({name : '게시물갯수'}, function(error, result){ //db.counter내의 총게시물갯수를 찾음
        console.log(result.totalPost)
        var totalPostnumber = result.totalPost; //총게시물갯수를 변수에 저장
        db.collection('post').insertOne({ __id: totalPostnumber+1, 제목:req.body.title, 날짜:req.body.date }, function(error,result){ //db.post에 새 게시물 기록함
            console.log('저장완료');
            //counter라는 콜렉션에 있는 totalPost라는 항목도 1 증가시킨다(수정)
            db.collection('counter').updateOne({name:'게시물갯수'},{$inc : {totalPost:1}},function(error, result){ //완료되면 db.counter내의 총게시물 갯수+1
                if(error){return console.log(error)}
            })
        });
    });
});

```
operator 종류

$set(변경)  
$inc(증가)  
$min(기존값보다 적을 때만 변경)  
$rename(key값 이름변경 ) 
등등.. 

{$set: {totalPost : 바꿀 값 } }
{$inc: {totalPost : 기존값에 더해줄 값} }
