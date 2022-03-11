## 📆 2022-03-11 (금) TIL

### 🔥 오늘 한 일 <br>
 
- 기존 api호출하던 방식을 ```useQuery```로 수정 (인자로 match.params.id) 넘기기 
- 현재까지는 useQuery로 불러온 데이터를 보여줬고, 데이터를 수정 후 post할때는  ```mutate```를 사용할 것이다.
 
### 1. 논문 수정페이지에서, 저장된 논문 개별로 불러오는 코드를 useQuery로 교체함

#### 커스텀 hooks를 만든다. 

publicationHooks.ts

```js
import {useQuery} from "react-query";
import {getResearchContent} from "../../../lib/apiHomepage"
// 위의 불러오는 getResearchContent 내용은 아래와 같다. 

//논문 하나를 조회 
export const getResearchContent: Function = async (researchId, token: string = TOKEN) => { //여기서 researchId가 match.params.id 이다.
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
  
  
기존의 HomepagePublicationUpdate.js 코드를 .tsx로 교체했다.  
아래는 기존의 api 호출하는 코드이다.   

```js
       useEffect(() => {
         apiHomepage.getResearchContent(match.params.id)
         .then(result => {
             setPublicView(result.data.data); //데이터를 받아와서 publicview에 넣어준다.
         })
         .catch(error => {
             // console.log('연구결과 수정 불러오기 실패', error)
             })   
         },[researchData])
```

⬇️⬇️⬇️⬇️⬇️⬇️
<u>HomepagePublicationUpdate.tsx 코드</u>

```js
import React, { useEffect } from "react";
import { withRouter, useLocation, useHistory, RouteComponentProps} from 'react-router-dom';


// 아래 3개 코드를 추가한다. 
import { useSelector } from "react-redux"; </u>
import {useGetResearchContent, useSetResearch} from "../../api/hooks/publication/publicationHooks";
import {RootState} from "../../Redux";


const HomepagePublicationsUpdate = ({match}:RouteComponentProps<MatchParams>)=> {
  const {user} = useSelector((state: RootState) => state.auth); // 로그인 상태값을 리덕스에서 받아온다. 
  const {data: publicData} = useGetResearchContent(match.params.id, user?.accessToken) // 기존의 API가 대체되는 코드다. 받아온 결과값은 publicData로 사용가능하다.
  
return(
 <>
 <input value={publicData?.researchId} /> // 받아온 데이터 value를 표기한다.
 <input value={publicData?.researchTitle} />
 <input value={publicData?.journal} />
 </>

export default withRouter(HomepagePublicationsUpdate);
```
  
  
  

