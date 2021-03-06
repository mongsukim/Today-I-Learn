## ๐ 2022-03-11 (๊ธ) TIL

### ๐ฅ ์ค๋ ํ ์ผ <br>
 
- ๊ธฐ์กด apiํธ์ถํ๋ ๋ฐฉ์์ ```useQuery```๋ก ์์  (์ธ์๋ก match.params.id) ๋๊ธฐ๊ธฐ 
- ํ์ฌ๊น์ง๋ useQuery๋ก ๋ถ๋ฌ์จ ๋ฐ์ดํฐ๋ฅผ ๋ณด์ฌ์คฌ๊ณ , ๋ฐ์ดํฐ๋ฅผ ์์  ํ postํ ๋๋  ```mutate```๋ฅผ ์ฌ์ฉํ  ๊ฒ์ด๋ค.
- ํ์์คํฌ๋ฆฝํธ์์ ํ์ ๋ด๋ก์์ด๋ผ๋๊ฑธ ์ ํด ๋ณด์๋ค.


### 1. ๋ผ๋ฌธ ์์ ํ์ด์ง์์, ์ ์ฅ๋ ๋ผ๋ฌธ ๊ฐ๋ณ๋ก ๋ถ๋ฌ์ค๋ ์ฝ๋๋ฅผ useQuery๋ก ๊ต์ฒดํจ

#### ์ปค์คํ hooks๋ฅผ ๋ง๋ ๋ค. 

publicationHooks.ts

```js
import {useQuery} from "react-query";
import {getResearchContent} from "../../../lib/apiHomepage"
// ์์ ๋ถ๋ฌ์ค๋ getResearchContent ๋ด์ฉ์ ์๋์ ๊ฐ๋ค. 

//๋ผ๋ฌธ ํ๋๋ฅผ ์กฐํ 
export const getResearchContent: Function = async (researchId, token: string = TOKEN) => { //์ฌ๊ธฐ์ researchId๊ฐ match.params.id ์ด๋ค.
    return await axios.get(APIADDRESS + "/home/getResearchContent/" + researchId + "?accessToken=" + token);
};

/////

export enum ResearchContentKeysEnum{
    ResearchContent = 'researchContent'
}

export const useGetResearchContent = (researchId, token: string | undefined) => {
    return useQuery(
        ResearchContentKeysEnum.ResearchContent, async() =>{
            const res = await getResearchContent(researchId, token)
            console.log('getResearchContent',res.data.data)
            return res.data.data as ResearchContent
        }
    )
}
```

.types.ts

```js
export interface ResearchContent{
    "researchId": number,
    "journal": string,
    "link": string,
    "hasLink": boolean
    "category": string,
    "userSeq": number,
    "pmid": string,
    "author": string,
    "regDttm": string,
    "updDttm": string,
    "pubDttm": string,
    "pubDttm2": string,
    "researchTitle": string,
    "researchContent": string,
}
  
  ```
  
  
๊ธฐ์กด์ HomepagePublicationUpdate.js ์ฝ๋๋ฅผ .tsx๋ก ๊ต์ฒดํ๋ค.  
์๋๋ ๊ธฐ์กด์ api ํธ์ถํ๋ ์ฝ๋์ด๋ค.   

```js
       useEffect(() => {
         apiHomepage.getResearchContent(match.params.id)
         .then(result => {
             setPublicView(result.data.data); //๋ฐ์ดํฐ๋ฅผ ๋ฐ์์์ publicview์ ๋ฃ์ด์ค๋ค.
         })
         .catch(error => {
             // console.log('์ฐ๊ตฌ๊ฒฐ๊ณผ ์์  ๋ถ๋ฌ์ค๊ธฐ ์คํจ', error)
             })   
         },[researchData])
```

โฌ๏ธโฌ๏ธโฌ๏ธโฌ๏ธโฌ๏ธโฌ๏ธ
<u>HomepagePublicationUpdate.tsx ์ฝ๋</u>

```js
import React, { useEffect } from "react";
import { withRouter, useLocation, useHistory, RouteComponentProps} from 'react-router-dom';


// ์๋ 3๊ฐ ์ฝ๋๋ฅผ ์ถ๊ฐํ๋ค. 
import { useSelector } from "react-redux"; </u>
import {useGetResearchContent, useSetResearch} from "../../api/hooks/publication/publicationHooks";
import {RootState} from "../../Redux";


const HomepagePublicationsUpdate = ({match}:RouteComponentProps<MatchParams>)=> {
  const {user} = useSelector((state: RootState) => state.auth); // ๋ก๊ทธ์ธ ์ํ๊ฐ์ ๋ฆฌ๋์ค์์ ๋ฐ์์จ๋ค. 
  const {data: publicData} = useGetResearchContent(match.params.id, user?.accessToken) // ๊ธฐ์กด์ API๊ฐ ๋์ฒด๋๋ ์ฝ๋๋ค. ๋ฐ์์จ ๊ฒฐ๊ณผ๊ฐ์ publicData๋ก ์ฌ์ฉ๊ฐ๋ฅํ๋ค.
  
return(
 <>
 <input value={publicData?.researchId} /> // ๋ฐ์์จ ๋ฐ์ดํฐ value๋ฅผ ํ๊ธฐํ๋ค.
 <input value={publicData?.researchTitle} />
 <input value={publicData?.journal} />
 </>

export default withRouter(HomepagePublicationsUpdate);
```
  
  
- ๋ฌธ์ ์  : ๊ธฐ์กด์ input onChange๊ฐ ์๋์ด์ , ๋ฐ์์จ ๋ฐ์ดํฐ๋ฅผ ์์ฒ๋ผ ๊ทธ๋๋ก ์ฐ๋ฉด input ์์ ์ด ์๋์๋ค.
๋๋ฌธ์ ๋ฐ์์จ ๋ฐ์ดํฐ๋ฅผ ๊ธฐ์กด์ฒ๋ผ ```publicView```์ ๋ด์ ์ฌ์ฉ ํด์ผ ํ๋ค. 

- onChange๊ฐ ๋๋๋ก ์ฝ๋๋ฅผ ๋ค์ ์์  ์ค ... 


