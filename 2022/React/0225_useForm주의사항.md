## 📆 2022-02-25 (금) TIL

### 🔥 오늘 한 일 <br>
 
- ```input type=file ``` 로 업로드한 것을 미리보기 하는 기능을 붙였다. 

- 기존에 발행한 데이터가 ```quill```에디터에서 수정이 안되는 문제를 해결했다.

- useForm이 왜 안되지 고민했는데, api 요청보낼때 **formdata** 로 보내는것을 계속 잊는다. 기억 잘 하자!
 
 
### 1. input type="file"로 선택한 파일을 미리보기 해보자.
```js

const [imageSrc, setImageSrc] = useState('');
    const encodeFileToBase64 = (fileBlob) => { 
        const reader = new FileReader(); 
        reader.readAsDataURL(fileBlob); 
        return new Promise((resolve) => { 
            reader.onload = () => { 
                setImageSrc(reader.result); 
                resolve(); 
            }; 
        }); 
    };

/// return 된 컴포넌트 생략 .. 

 <div className="preview">
  {imageSrc && <img src={imageSrc} alt="preview-img" />} 
 </div>
 <input
  type="file"
  name="thumnail"
  onChange={  //onChange시에 실행될 함수를 2개 넣는 방법을 알게 되었다!
  (e) => 
  { 
  encodeFileToBase64(e.target.files[0])
  setValue("file", e.target.files[0])
 }
}
 ref={register()} 
/>
  
  
  ```
  
  #### 2. 수정이 안되던 문제 해결한 방법
  
   - quillvalue 세팅을 따로 해 주었다.
  
  ```js
   setQuillValue(result.data.data.newsContents)
   setNewsView(result.data.data);
  ```
  
  
  #### 3. Formdata
  
  ```js
  
  
//뉴스 저장된거 불러와서 보여주기
    useEffect(()=>{
        var strLang = "KR";
        apiNews.getNewsContentLang(strLang, match.params.id)
        .then( result => {
            setQuillValue(result.data.data.newsContents) //quillvalue를 따로 세팅해준다.
           // console.log('setQuillValue',result.data.data.newsContents)
           console.log('setNewsView',result.data.data)

            setNewsView(result.data.data);
        })
        .catch(error => {
            console.log('불러오기실패',error)
        })
    },[])
    
    const UpdateForm = data => {
        const formdata = new FormData();
        const newsId = Number(data.newsId);
        formdata.append("newsId", newsId);
        formdata.append("newsTitle", data.newsTitle);
        formdata.append("newsContents", quillValue); //quillValue를 제출한다.
        formdata.append("link", data.link);
        formdata.append("category", data.category);
        formdata.append("file", data.file);
        apiNews.updateNewsKR_(formdata) // **formdata와 data는 다르니 주의한다!**
        .then( response => {
            alert('뉴스 수정을 완료했습니다')
            console.log('뉴스수정 성공~' ,response)
            
            history.push({
                pathname: '/HomepageNewsLists_KR'
            })
        })
        .catch(error => {
            alert('뉴스 수정 실패', error)
            console.log('뉴스업데이트 실패',error)
            console.log('데이터내용',data)
        })
      }
      
      ...... return 한 컴포넌트 생략 
       <Controller
                    defaultValue={newsContents}
                    name="newsContents"
                    control={control}                    
                    render={()=>(
                        <ReactQuill
                            theme="snow"
                            value={quillValue}
                            onChange={setQuillValue}
                            modules={quillModules}                                     
                        />)}
                    /> 
      
  ```
  
