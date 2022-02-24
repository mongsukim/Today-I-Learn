## 📆 2022-02-24 (목) TIL

### 🔥 오늘 한 일 <br>
 
- 오늘은 몇달 간 지속되던 오류를 고쳤다.  
react-html-parser가 로컬환경에선 잘되는데,  
배포시에 오류가 나는것을 확인하고, 교체했다.  

- ```&&``` 연산자를 알게 되었다. 
 
### 1. 함수형 컴포넌트에 조건문을 달아 다르게 return하기

```js

const HomepageContactReplyReplyEdit = ({match}) => {


  interface UserType{
    contactCategory: string;
    contactChkReply:string;
    contactRegDttm:string;
    contactTitle:string;
    contactUserName:string;
    contactUserEmail:string;
    contactFileUri:string;
    contactContents:string;
    contactUserPhone:string;
    replyFile:string;       //댓글이 달리면 replyFile, replyUserName등이 오고, 댓글이 없으면 오지 않는다.
    replyFileUri:string;
    replyRegDttm:string;
    replyUserName:string;
    originSeq:number;
    contactSeq:number;
  }
  
  const [users, setUsers] = useState<UserType[]>(); 
  
  useEffect(()=>{
    apiHomepage.getContactContents(TOKEN, match.params.id)
    .then(result=>{
        setUsers(result.data.data);
    }) 
    .catch(error => {
        console.log(' 불러오기 실패', error)
    })   
},[]);


if(users && users[0].replyUserEmail) // if문에 따라 return을 다르게 해준다.
  return (
    <div>
      <h2>관리자 답변 리스트</h2>
          <div>
              {users && users.map((Item:any)=>(
                <ReplyComponent key={Item.id} {...Item}  />
              ))} 
          </div>
    </div>  
  )
  
const [openAddReply, SetOpenAddReply] = useState(false); 
const openModalReply = () =>{
  SetOpenAddReply(true)
}

  return ( //replyUserEmail이 없으면 이것이 리턴된다.
  <>
  관리자 답변이 없습니다 
    <Button 
    type="primary" 
      className={Styles.admin_btn_margin}
      onClick={openModalReply}>답변 달기</Button>
    { openAddReply ? //openAddReply의 값이 true면 컴포넌트 보이고,
      <HomepageContactReplyAddReply /> :
      <></> //false(기본값)이면 컴포넌트가 보이지 않는다.
    }
  </>
  )
};
  
  
  ```
  
  #### 2. &&연산자 
  
  
  #### 3. react-html-parser대신 dompurify 사용하기.
  
  
  npm으로 ```dompurify``` 를 설치한다.
  
  ```js
  
  import DOMPurify from "dompurify";
  // ..생략
   <span dangerouslySetInnerHTML={{__html:DOMPurify.sanitize(question.contactContents)}}></span>
  ```
  
