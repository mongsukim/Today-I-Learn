## ๐ 2022-03-14 (์) TIL

### ๐ฅ ์ค๋ ํ ์ผ <br>
 
- ```useForm```์ ์ ํจ์ฑ ๊ฒ์ฌ๋ฅผ ์ถ๊ฐํ๋ค. 



### 1. ์ ํจ์ฑ ๊ฒ์ฌ ์ถ๊ฐ

#### ref={register({pattern:value,message}})}์ ์ถ๊ฐํ๋ค


```js

import { useForm, Controller } from "react-hook-form";

    const { register, handleSubmit, errors, control } = useForm();
    const validPMID = /PMID:/   //์ด๊ณณ์ ์ ๊ทํํ์๊ณผ ๊ฐ์ ๊ท์น์ ๋ฃ๋๋ค

return(
<Form onFinish={handleSubmit(UpdateForm)}
  <Form.Item label="(PMID:์ซ์)">
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
                                value: validPMID ,  // pattern ์์ฑ์ value๋ฅผ ์ถ๊ฐํ๊ณ  , ์ค๋ฅ์ ๋ํ๋ผ ๋ฉ์์ง๋ฅผ ์ ๋๋ค
                                message:'PMID: ์ซ์ ๋ก ์๋ ฅํด์ฃผ์ธ์',
                        }
                    })}     
                    />
                    {
                    errors.pmid && errors.pmid.type === "required" &&
                    <p className="require_txt">PMID๋ฅผ ์๋ ฅํด์ฃผ์ธ์</p>
                    }
                    {
                        errors.pmid &&
                        <p className="require_txt">PMID: ์ซ์ ๋ก ์๋ ฅํด์ฃผ์ธ์</p> //์ ํจ์ฑ ๊ฒ์ฌ๋ฅผ ํต๊ณผํ์ง ๋ชปํ์๋ ๋จ๋ ์ค๋ฅ๋ฌธ๊ตฌ.
                    }
  </Form.Item>
</Form>

```
  
