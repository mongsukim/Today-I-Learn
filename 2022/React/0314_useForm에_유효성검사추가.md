## 📆 2022-03-14 (월) TIL

### 🔥 오늘 한 일 <br>
 
- ```useForm```에 유효성 검사를 추가했다. 



### 1. 유효성 검사 추가

#### ref={register({pattern:value,message}})}을 추가한다


```js

import { useForm, Controller } from "react-hook-form";

    const { register, handleSubmit, errors, control } = useForm();
    const validPMID = /PMID:/   //이곳에 정규표현식과 같은 규칙을 넣는다

return(
<Form onFinish={handleSubmit(UpdateForm)}
  <Form.Item label="(PMID:숫자)">
                    <input 
                        className="inputsize"
                        placeholder="PMID:0000"
                        type="text"
                        name="pmid" 
                        value={pmid}
                        onChange={onChange}
                        ref={register({
                            required:true,
                            pattern:{
                                value: validPMID ,  // pattern 속성에 value를 추가하고 , 오류시 나타낼 메시지를 적는다
                                message:'PMID: 숫자 로 입력해주세요',
                        }
                    })}     
                    />
                    {
                    errors.pmid && errors.pmid.type === "required" &&
                    <p className="require_txt">PMID를 입력해주세요</p>
                    }
                    {
                        errors.pmid &&
                        <p className="require_txt">PMID: 숫자 로 입력해주세요</p> //유효성 검사를 통과하지 못했을때 뜨는 오류문구.
                    }
  </Form.Item>
</Form>

```
  
