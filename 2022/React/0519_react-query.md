## ๐ 2022-05-19 (๋ชฉ) TIL

### ๐ฅ ์ค๋ ํ ์ผ <br>

- ํํ์ด์ง serialํ์ด์ง์ ๊ทธ ํ์ด์ง๋ฅผ ๊ด๋ฆฌํ๋ admin console์ ํ์ด์ง๋ค์ ์์ฑํ๋ค.
- ์ํ ๊ด๋ฆฌ๋ Redux๋์  react-query๋ฅผ ์ฌ์ฉํ๋ค.  
- ๋ก๊ทธ์ธ ์ ๋ณด ์์๋๋ง ์ฟผ๋ฆฌ ์์ฒญ, ๋ฐ์ดํฐ๊ฐ ๋ฐ๋ ๋๋ง๋ค ์๋ก๋ถ๋ฌ์ค๋๋ก key๊ฐ ๋ฃ๊ธฐ ๋ฑ   
์ ์  ์๋ จ๋๊ฐ ์ฌ๋ผ๊ฐ๊ณ  ์๋ค. 

## ์๋ก ์๊ฒ ๋ ๊ฒ  

##### ๋ก๊ทธ์ธ ํ ํฐ์ด ์์๋๋ง react-query๋ก get์์ฒญ์ ํ๊ณ  ์ถ์ด์ ์ฐพ์๋ณด๋,    
ํธ์ถ ์ฝ๋์ ์ต์์ ์ค ์ ์์๋ค.   

```js
        {
            enabled: !!token,
        }
```

๋ป: ํ ํฐ์ด ์กด์ฌํ ๋๋ง ์ฟผ๋ฆฌ์์ฒญ์ ํ๋ค. 

<hr/>

#### ๋ฐ์ดํฐ๊ฐ ์์ ๋ ์ดํ ์๋ก๊ณ ์นจ์ ์ํด ์์๋ก ๊ฐ์ ์ฃผ๊ธฐ

```[MDUserKeysEnum.MD_CommentList, data]``` ์์  
data๋ ๋ฐฐ์ด์์ ํค๊ฐ์ผ๋ก ์์๋ก ๋ฃ์ ๊ฒ์ด๋ค.   
๊ทธ๋ฌ๋ฉด ๋ง์ฝ ์ฝ๋ฉํธ๊ฐ ์์ ๋ฌ์ ์ ์ฝ๋ฉํธ ๋ชฉ๋ก์ด ์๋ก๊ณ ์นจ์ด ๋๋ค   
(์ ์ ํ ๋ฐ์ดํฐ๊ฐ ์จ๋ค)  

  

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
