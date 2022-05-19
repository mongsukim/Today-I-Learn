## 📆 2022-05-19 (목) TIL

### 🔥 오늘 한 일 <br>

- 홈페이지 serial페이지와 그 페이지를 관리하는 admin console의 페이지들을 완성했다.
- 상태 관리는 Redux대신 react-query를 사용했다.  
- 로그인 정보 있을때만 쿼리 요청, 데이터가 바뀔 때마다 새로불러오도록 key값 넣기 등   
점점 숙련도가 올라가고 있다. 

## 새로 알게 된 것  

##### 로그인 토큰이 있을때만 react-query로 get요청을 하고 싶어서 찾아보니,    
호출 코드에 옵션을 줄 수 있었다.   

```js
        {
            enabled: !!token,
        }
```

뜻: 토큰이 존재할때만 쿼리요청을 한다. 

<hr/>

#### 데이터가 수정된 이후 새로고침을 위해 임의로 값을 주기

```[MDUserKeysEnum.MD_CommentList, data]``` 에서  
data는 배열안의 키값으로 임의로 넣은 것이다.   
그러면 만약 코멘트가 수정됬을 시 코멘트 목록이 새로고침이 된다   
(신선한 데이터가 온다)  

  

```js
export const useGetMDUserComment = (data, token: string | undefined) => {
	return useQuery([MDUserKeysEnum.MD_CommentList, data], async () => {
		const res = await getEntireProductSerialCommentAndReplyList(data, token);
		// console.log('useGetMDUserComment res', res);
		// console.log('useGetMDUserComment res.data.data', res.data.data);

		return res.data.data as MD_UserCommentList[];
	});
};
```
