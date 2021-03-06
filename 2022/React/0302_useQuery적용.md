## ๐ 2022-03-02 (์) TIL

### ๐ฅ ์ค๋ ํ ์ผ <br>
 
- ์ธ์์ธ๊ณ ๋ฐ๊ธฐ(nodejs, javascript๋ก ์ด๋ฃจ์ด์ง ํ๋ก์ ํธ)
- ๊ธฐ์กด apiํธ์ถํ๋ ๋ฐฉ์์ ```useQuery```๋ก ์์  
 
 
### 1. ๋ผ๋ฌธ ํ์ด์ง / ํ๊ธ๋ด์ค ํ์ด์ง / contact๊ฒ์ํ ํ์ด์ง์ ๊ธ ๋ถ๋ฌ์ค๋ ๋ฆฌ์คํธ๋ฅผ useQuery๋ก ๊ต์ฒดํจ

- ๋ค๋ง ```post``` ํ๋ ์ฝ๋๋ ```match.param.id``` ๋ฌธ์ ๋ก ์์ง ์ ์ฉํ์ง ๋ชปํ๋ค.
- ๋ผ์ฐํ ์ฒ๋ฆฌ๋ฅผ ์ด๋ป๊ฒ ํด์ผํ๋์ง ๊ณต๋ถ๋ฅผ ํด์ผํ๋ค.. 

#### ์ปค์คํ hooks๋ฅผ ๋ง๋ ๋ค. 

publicationHooks.ts

```js
import {useQuery} from "react-query";
import {getResearchList} from "../../../lib/apiHomepage"
// ์์ ๋ถ๋ฌ์ค๋ getResearchList ๋ด์ฉ์ ์๋์ ๊ฐ๋ค. 
// apiHomepae ํ์ผ์๋ ์๋์ฒ๋ผ ์ฌ๋ฌ api๋ค์ด ๋ชจ์ฌ์๋ค.

export const getResearchList:Function = async ( token: string = TOKEN ) => {
	console.log("APIํจ์ /home/getResearchList");
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
  
๊ธฐ์กด์ HomepagePublicationLists.js ์ฝ๋๋ฅผ .tsx๋ก ๊ต์ฒดํ๋ค.  
์๋๋ ๊ธฐ์กด์ api ํธ์ถํ๋ ์ฝ๋์ด๋ค.   

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
    .catch(err=>console.log('์๋ฌ',err));
```

โฌ๏ธโฌ๏ธโฌ๏ธโฌ๏ธโฌ๏ธโฌ๏ธ
<u>HomepagePublicationLists.tsx ์ฝ๋</u>

```js
import React, { useEffect,useState,FC } from "react";
import { Table, Button  } from 'antd';
import { Link } from 'react-router-dom';
import Styles from "../../scss/App.module.scss";
import * as apiHomepage from '../../lib/apiHomepage';

// ์๋ 3๊ฐ ์ฝ๋๋ฅผ ์ถ๊ฐํ๋ค. 
import { useSelector } from "react-redux"; </u>
import {useGetResearchList} from "../../api/hooks/publication/publicationHooks";
import {RootState} from "../../Redux";


const HomepagePublicationsLists: FC = () => { // .tsx๋ก ๋ณ๊ฒฝํ๋ฉฐ : FC๋ฅผ ์จ์ค๋ค. 
  const {user} = useSelector((state: RootState) => state.auth); // ๋ก๊ทธ์ธ ์ํ๊ฐ์ ๋ฆฌ๋์ค์์ ๋ฐ์์จ๋ค. 
  const {data: publicData} = useGetResearchList(user?.accessToken) // ๊ธฐ์กด์ API๊ฐ ๋์ฒด๋๋ ์ฝ๋๋ค. ๋ฐ์์จ ๊ฒฐ๊ณผ๊ฐ์ publicData๋ก ์ฌ์ฉ๊ฐ๋ฅํ๋ค.
  
  const [ publicView, setPublicView ] = useState<KeyArr[]>(); 
  // ๊ธฐ์กด์ ๋น๊ฐ์ธ publicView์ KeyArr๋ผ๋ ๋ฐฐ์ด๋ก ์๋ฐ์ดํธ๋๋ค. ํ์์ ์ง์ ํด์ค๋ค.

interface KeyArr {
  pmid: string,
  researchId: number,
  researchTitle:  string,
  pubDttm: string,
  key: number
}

  useEffect(() => {
    const KeyArr = publicData?.map((publicData,index) => { //useQuery์์ ๋ฐ์์จ ๊ฒฐ๊ณผ๊ฐ์ .map์ผ๋ก ๋๋ฆฐ๋ค.
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
    if(window.confirm(`${researchId}๋ฒ ๋ผ๋ฌธ์ ์ญ์ ํ์๊ฒ ์ต๋๊น?`)){
        try{
          apiHomepage.deleteResearch(researchId);
          alert('๋ผ๋ฌธ์ด ์ญ์ ๋์์ต๋๋ค')
          window.location.replace("/HomepagePublicationsLists")
        } catch(error){
            alert('๋ผ๋ฌธ ์ญ์  ์คํจ')
            console.log(' ์คํจ', error)
        }
      } else{
        alert('์ทจ์ํ์ต๋๋ค.')
      }
    }
  
        return (
            <div className={Styles.page_wrap}>
                <h2 className={Styles.admin_title}>Publications Lists</h2>
                <p className={Styles.admin_txt}>์ฐ๊ตฌ์ฑ๊ณผ๋ฅผ ์กฐํํ๊ณ  ์์ ํฉ๋๋ค.</p>               
                <Table
                columns={columns}
                dataSource={publicView}
                />
                <Button type="primary" className={Styles.admin_btn_margin}>
                  <Link to="/HomepagePublicationsRegist">๋ผ๋ฌธ ์ถ๊ฐ</Link></Button>
                {/* <Button onClick={publicDelete} type="primary" danger >
                  ์ ํ์ญ์ 
                </Button>    */}
            </div>
        );
    
}

export default HomepagePublicationsLists;

```
  
  
  
### 2.  ์ธ์์ธ๊ณ ๋ฐ๊ธฐ

- ๋์ค ์ฃผ์, ๊ธฐ๋ณธ ์ค์  ์ธํ ๋ฑ ..
- ๋์ฑ ์์ธํ๊ฑด ๋ด์ผ 3๋ช์ด ๊ฐ์ด ์ธ์์ธ๊ณ ๋ฐ๊ธฐ๋ก ํ์๋ค. 

