## ๐ 2022-02-25 (๊ธ) TIL

### ๐ฅ ์ค๋ ํ ์ผ <br>
 
- ```input type=file ``` ๋ก ์๋ก๋ํ ๊ฒ์ ๋ฏธ๋ฆฌ๋ณด๊ธฐ ํ๋ ๊ธฐ๋ฅ์ ๋ถ์๋ค. 

- ๊ธฐ์กด์ ๋ฐํํ ๋ฐ์ดํฐ๊ฐ ```quill```์๋ํฐ์์ ์์ ์ด ์๋๋ ๋ฌธ์ ๋ฅผ ํด๊ฒฐํ๋ค.

- useForm์ด ์ ์๋์ง ๊ณ ๋ฏผํ๋๋ฐ, api ์์ฒญ๋ณด๋ผ๋ **formdata** ๋ก ๋ณด๋ด๋๊ฒ์ ๊ณ์ ์๋๋ค. ๊ธฐ์ต ์ ํ์!
 
 
### 1. input type="file"๋ก ์ ํํ ํ์ผ์ ๋ฏธ๋ฆฌ๋ณด๊ธฐ ํด๋ณด์.
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

/// return ๋ ์ปดํฌ๋ํธ ์๋ต .. 

 <div className="preview">
  {imageSrc && <img src={imageSrc} alt="preview-img" />} 
 </div>
 <input
  type="file"
  name="thumnail"
  onChange={  //onChange์์ ์คํ๋  ํจ์๋ฅผ 2๊ฐ ๋ฃ๋ ๋ฐฉ๋ฒ์ ์๊ฒ ๋์๋ค!
  (e) => 
  { 
  encodeFileToBase64(e.target.files[0])
  setValue("file", e.target.files[0])
 }
}
 ref={register()} 
/>
  
  
  ```
  
  #### 2. ์์ ์ด ์๋๋ ๋ฌธ์  ํด๊ฒฐํ ๋ฐฉ๋ฒ
  
   - quillvalue ์ธํ์ ๋ฐ๋ก ํด ์ฃผ์๋ค.
  
  ```js
   setQuillValue(result.data.data.newsContents)
   setNewsView(result.data.data);
  ```
  
  
  #### 3. Formdata
  
  ```js
  
  
//๋ด์ค ์ ์ฅ๋๊ฑฐ ๋ถ๋ฌ์์ ๋ณด์ฌ์ฃผ๊ธฐ
    useEffect(()=>{
        var strLang = "KR";
        apiNews.getNewsContentLang(strLang, match.params.id)
        .then( result => {
            setQuillValue(result.data.data.newsContents) //quillvalue๋ฅผ ๋ฐ๋ก ์ธํํด์ค๋ค.
           // console.log('setQuillValue',result.data.data.newsContents)
           console.log('setNewsView',result.data.data)

            setNewsView(result.data.data);
        })
        .catch(error => {
            console.log('๋ถ๋ฌ์ค๊ธฐ์คํจ',error)
        })
    },[])
    
    const UpdateForm = data => {
        const formdata = new FormData();
        const newsId = Number(data.newsId);
        formdata.append("newsId", newsId);
        formdata.append("newsTitle", data.newsTitle);
        formdata.append("newsContents", quillValue); //quillValue๋ฅผ ์ ์ถํ๋ค.
        formdata.append("link", data.link);
        formdata.append("category", data.category);
        formdata.append("file", data.file);
        apiNews.updateNewsKR_(formdata) // **formdata์ data๋ ๋ค๋ฅด๋ ์ฃผ์ํ๋ค!**
        .then( response => {
            alert('๋ด์ค ์์ ์ ์๋ฃํ์ต๋๋ค')
            console.log('๋ด์ค์์  ์ฑ๊ณต~' ,response)
            
            history.push({
                pathname: '/HomepageNewsLists_KR'
            })
        })
        .catch(error => {
            alert('๋ด์ค ์์  ์คํจ', error)
            console.log('๋ด์ค์๋ฐ์ดํธ ์คํจ',error)
            console.log('๋ฐ์ดํฐ๋ด์ฉ',data)
        })
      }
      
      ...... return ํ ์ปดํฌ๋ํธ ์๋ต 
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
  
