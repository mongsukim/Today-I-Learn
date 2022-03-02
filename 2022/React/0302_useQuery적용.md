## 📆 2022-03-02 (수) TIL

### 🔥 오늘 한 일 <br>
 
- 인수인계 받기(nodejs, javascript로 이루어진 프로젝트)
- 기존 api호출하던 방식을 ```useQuery```로 수정 
 
 
### 1. 논문 페이지 / 한글뉴스 페이지 / contact게시판 페이지의 글 불러오는 리스트를 useQuery로 교체함

- 다만 ```post``` 하는 코드는 ```match.param.id``` 문제로 아직 적용하진 못했다.
- 라우팅 처리를 어떻게 해야하는지 공부를 해야한다.. 

#### 커스텀 hooks를 만든다. 

publicationHooks.ts

```js
import {useQuery} from "react-query";
import {getResearchList} from "../../../lib/apiHomepage"
// 위의 불러오는 getResearchList 내용은 아래와 같다. 
// apiHomepae 파일에는 아래처럼 여러 api들이 모여있다.

export const getResearchList:Function = async ( token: string = TOKEN ) => {
	console.log("API함수 /home/getResearchList");
	return await axios.get(APIADDRESS + "/home/getResearchList?accessToken="+token);
};
/////

export enum ResearchKeysEnum {
    ResearchList = 'researchList',
}

export const useGetResearchList = ( token: string | undefined) => {
    return useQuery(
        ResearchKeysEnum.ResearchList, async () => {
            const res = await getResearchList(token)
           // console.log('public res',res.data.data)
            return res.data.data as ResearchList[]
        }
    )
}

interface ResearchList {
    "author": string,
    "category": string,
    "hasLink": boolean,
    "journal": string,
    "link": string,
    "pmid": string,
    "pubDttm": string,
    "pubDttm2": string,
    "regDttm": string,
    "researchContent": string,
    "researchId": number,
    "researchTitle": string,
    "updDttm": string,
    "userSeq": number
}
  
  ```
  
기존의 HomepagePublicationLists.js 코드를 .tsx로 교체했다.  
아래는 기존의 api 호출하는 코드이다.   

```js
  useEffect(() => {
        apiHomepage.getResearchList()
    .then(res => {
      const keyArr = res.data.data.map((data,index) => {
        return{
          pmid: data.pmid,
          researchId: data.researchId,
          researchTitle: data.researchTitle,
          pubDttm: data.pubDttm,
          key: index
        }
      })
      setPublicView(keyArr)
    })
    .catch(err=>console.log('에러',err));
```

⬇️⬇️⬇️⬇️⬇️⬇️
<u>HomepagePublicationLists.tsx 코드</u>

```js
import React, { useEffect,useState,FC } from "react";
import { Table, Button  } from 'antd';
import { Link } from 'react-router-dom';
import Styles from "../../scss/App.module.scss";
import * as apiHomepage from '../../lib/apiHomepage';

// 아래 3개 코드를 추가한다. 
import { useSelector } from "react-redux"; </u>
import {useGetResearchList} from "../../api/hooks/publication/publicationHooks";
import {RootState} from "../../Redux";


const HomepagePublicationsLists: FC = () => { // .tsx로 변경하며 : FC를 써준다. 
  const {user} = useSelector((state: RootState) => state.auth); // 로그인 상태값을 리덕스에서 받아온다. 
  const {data: publicData} = useGetResearchList(user?.accessToken) // 기존의 API가 대체되는 코드다. 받아온 결과값은 publicData로 사용가능하다.
  
  const [ publicView, setPublicView ] = useState<KeyArr[]>(); 
  // 기존에 빈값인 publicView은 KeyArr라는 배열로 업데이트된다. 타입을 지정해준다.

interface KeyArr {
  pmid: string,
  researchId: number,
  researchTitle:  string,
  pubDttm: string,
  key: number
}

  useEffect(() => {
    const KeyArr = publicData?.map((publicData,index) => { //useQuery에서 받아온 결과값을 .map으로 돌린다.
      return{
        pmid: publicData.pmid,
        researchId: publicData.researchId,
        researchTitle: publicData.researchTitle,
        pubDttm: publicData.pubDttm,
        key: index
      }
    })
    console.log('public KeyArr',KeyArr)
    setPublicView(KeyArr)
    }, [publicData]);
  //console.log("publicview",publicView)

  const columns = [
    {
      title: 'ID',
      dataIndex: 'researchId',
      key: 'researchId',      
    },
    {
      title: 'PMID',
      dataIndex: 'pmid',
      key: 'pmid',      
    },
    {
      title: 'Research Title',
      dataIndex: 'researchTitle',
      key: 'researchTitle',
      render : (text, publicView) => 
        <Link to={{pathname:"/HomepagePublicationsUpdate/"+(publicView.researchId)}}>{text}
        </Link>      
    },
    {
        title: 'pubDttm',
        dataIndex: 'pubDttm',
        key: 'pubDttm',
    },    
    {
      title: 'Action',
      dataIndex: '',
      key: 'x',
      render: (publicView) => <a onClick={publicDelete.bind(this, publicView.researchId)}>Delete</a>,
    },
  ]; 
  
  function publicDelete(researchId) {
    if(window.confirm(`${researchId}번 논문을 삭제하시겠습니까?`)){
        try{
          apiHomepage.deleteResearch(researchId);
          alert('논문이 삭제되었습니다')
          window.location.replace("/HomepagePublicationsLists")
        } catch(error){
            alert('논문 삭제 실패')
            console.log(' 실패', error)
        }
      } else{
        alert('취소했습니다.')
      }
    }
  
        return (
            <div className={Styles.page_wrap}>
                <h2 className={Styles.admin_title}>Publications Lists</h2>
                <p className={Styles.admin_txt}>연구성과를 조회하고 수정합니다.</p>               
                <Table
                columns={columns}
                dataSource={publicView}
                />
                <Button type="primary" className={Styles.admin_btn_margin}>
                  <Link to="/HomepagePublicationsRegist">논문 추가</Link></Button>
                {/* <Button onClick={publicDelete} type="primary" danger >
                  선택삭제
                </Button>    */}
            </div>
        );
    
}

export default HomepagePublicationsLists;

```
  
  
  
### 2.  인수인계 받기

- 나스 주소, 기본 설정 세팅 등 ..
- 더욱 자세한건 내일 3명이 같이 인수인계 받기로 하였다. 

