
_땅콩코딩 typescript 유투브 강의를 보고 요약 하였습니다_.


변수 값에 데이터 타입을 지정 가능하다.

![20220117_135544](https://user-images.githubusercontent.com/29091608/149730407-d2265191-a6d5-423a-a733-35cea5bb74ab.jpg)

왼쪽 자바스크립트 코드의 경우 결과가 3과 5인 string이 합쳐진 35가 나올 것이다.

오른쪽에 타입스크립트를 보면 a,b는 숫자라고 명시 함.



#### 컴파일 : 어떤 언어의 코드를 다른 언어로 바꿔주는 변환과정.


타입 추론 (Type Inference)

a에 5(숫자)를 할당 하고, a에 'hello'라는 문자열을 다시 할당 하려면 오류가 생긴다.

재할당하려는 변수값이 숫자가 아니기 때문에 a변수에 할당될 수 없다.


![20220117_135823](https://user-images.githubusercontent.com/29091608/149730421-c4dc2a15-5881-4168-9be9-1c4e5b1baf7f.jpg)


## 타입 명시 (Type Annotations)


변수를 선언할 때, 변수 값의 타입을 명시함으로써 변수 값의 데이터 타입을 지정한다.

```js 
let x : string = '나는 영원한 문자열';
//이제 x의 변수는 string만 할당 가능하다.
```

함수의 값의 type을 선언할 수 있다.

만약 함수가 아무 값도 반환하지 않는다면? :void


아래 함수는 객체가 반환되는데, 반환되는 객체의 구조를 타입으로 지정할 수 있다.

![20220117_140519](https://user-images.githubusercontent.com/29091608/149730433-1fabf6a1-978c-462b-b1c3-61a3fa0574f9.jpg)



### Interface로 객체 구조를 정의하기

Interface 이름을 대문자로 작성



![20220117_141006](https://user-images.githubusercontent.com/29091608/149730439-162f9eef-d029-443b-806f-e994c2ed58b4.jpg)


interface를 type으로 가지는 값은

interface의 구조를 그 값으로 가지도록 강제된다.


![20220117_141107](https://user-images.githubusercontent.com/29091608/149730444-7af7c700-ee6e-4fa6-b121-339e0b1aceb5.jpg)


여기서 만약 return 값 안의 age를 지운다면?

아래처럼 오류 메시지가 뜬다.




![20220117_141146](https://user-images.githubusercontent.com/29091608/149730454-c3170d59-7619-4513-86fe-5b1dcbb88dbc.jpg)


age값이 있어도 되고, 없어도 되도록 만든다면?

### 옵셔널 기호인 ?를 붙인다 (선택적 프로퍼티)



![20220117_141305](https://user-images.githubusercontent.com/29091608/149730465-108fcad6-aa5d-4040-80e8-eed55e230ffd.jpg)

이제 age프로퍼티가 반환값에 없어도 컴파일 에러가 생기지 않는다.


## Student interface를 재사용 할 수 있다.

![20220117_141439](https://user-images.githubusercontent.com/29091608/149730472-cc2c81ef-46e6-43cc-b441-f0ae43716181.jpg)

컴파일 오류 생기지 않음


## Method(메소드)란?

### 객체 내에서 선언된 함수


![20220117_141600](https://user-images.githubusercontent.com/29091608/149730479-6a26f8f2-01c3-4730-ad98-74d9886f32f9.jpg)


화살표 함수도 사용 가능하다.



Read Only 속성

Readonly 프로퍼티는 읽기 전용 프로퍼티로

객체 생성시 할당된 프로퍼티의 값을 바꿀 수 없다.



![20220117_141830](https://user-images.githubusercontent.com/29091608/149730493-7603e7d6-0162-46d9-95aa-3ee8eaeb3b7d.jpg)



![20220117_141854](https://user-images.githubusercontent.com/29091608/149730867-f16038ce-e88b-40a9-9d1b-a47728f3fc7d.jpg)


재 할당 되지 않는다.


## 열거형(Enum)과 리터럴 타입

### Enum : 연관된 아이템들을 함께 묶어서 표현할 수 있는 수단.

만약 gender값의 Male, Female로 지정하고 싶다면 ?


![20220117_142216](https://user-images.githubusercontent.com/29091608/149731520-90f6e913-aa1d-40bf-b26d-c6d657426171.jpg)



![20220117_142330](https://user-images.githubusercontent.com/29091608/149731565-73126364-4757-44ac-8679-9b8188bb6d13.jpg)


![20220117_142344](https://user-images.githubusercontent.com/29091608/149731582-422c5671-999a-4858-bc5b-7cb6ecb344c3.jpg)


오른쪽의 js파일을 보면 숫자형 enum으로 되어 있다



![20220117_142503](https://user-images.githubusercontent.com/29091608/149731593-bd17801e-4fd6-457b-b001-482d38902d83.jpg)


문자열 열거형으로 바꾸기



![20220117_142554](https://user-images.githubusercontent.com/29091608/149731606-4a1bddc0-b807-44e5-93d2-46a7586034d9.jpg)


리터럴 타입



![20220117_142638](https://user-images.githubusercontent.com/29091608/149731617-a5dcf72d-4306-4079-baab-e588c27c125f.jpg)






