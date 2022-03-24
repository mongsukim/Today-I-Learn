# Search Index

- 자바스크립트로 query string 만들기 
```js
var 자료 = { 이름 : '값', 이름2: '값2' }
$.param(자료)
```

```js
$(폼태그).serialize()
```

#### 한글검색에 친화적인 Search index 생성 

몽고DB -> search -> Create Search Index   
 lucene.korean 로 설정 


```aggregate()``` 함수: 검색조건 여러개를 붙이고 싶을 때 유용하다.

aggregate() 안에 [ {검색조건1}, {검색조건2} ... ] 이렇게 조건을 여러개 집어넣을 수 있다. 

```js
app.get('/search', (req, res)=> {
  var searchRequirement = [
    {
      $search: {
        index: 'titleSearch',
        text: {
          query: req.query.value,
          path: '제목'  // 제목날짜 둘다 찾고 싶으면 ['제목', '날짜']
        }
      }
    },
    {$sort:{_id:1}} //검색결과를 숫자순으로 정렬해서 보여준다. 
  ]

  db.collection('post').aggregate(searchRequirement).toArray((error, result) => {
    console.log(result)
    res.render('resultpage.ejs', { posts: result });
  })
})
```
