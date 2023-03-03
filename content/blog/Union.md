---
title: Union Type을 오해했다
date: 2023-01-15
description: 잘 짚고 넘어가자
tags: [TypeScript]
---

# 🧨 Union Type을 오해했다

여태까지 저는 Union Type을 사용하면서 머릿 속으로 Union Type에 대해 이런 정의를 내렸습니다. **이 타입도 저 타입도 사용 가능하게 해주네. A | B면 A와 B타입 모두 사용할 수 있어** 라고요. 그리고 저는 오류를 마주할 수 밖에 없었습니다.

예를 들어 이런 타입이 있다고 가정합시다.

```javascript
interface User {
  name: string;
  age: string;
}

interface Account {
  accountNumber: number;
  create_at: string;
}
```

이 타입들에 Union type을 사용하면 다음과 같은 코드를 사용할 수 있을 것이라 생각해왔어요.

```javascript
function Handler(UserAccount: User | Account) {
  UserAccount.name;
  UserAccount.create.at;
}
```

그러나 UserAccount의 프로퍼티에 접근하려고 하면 자동완성 목록은 뜨지 않고 다음과 같은 오류가 발생합니다.

> error: Property 'name' does not exist on type 'A | B'. Property 'name' does not exist on type 'B'.

다음과 같은 타입을 사용할 수 있을 거라 기대하고 유니온을 사용했지만

```javascript
{
  name: string;
  age: string;
  accountNumber: number;
  create_at: string;
}
```

결과는 이랬던 것입니다.

```javascript
{
   텅!
}
```

![](https://velog.velcdn.com/images/seripark/post/5dd59dc6-d994-4bf0-85fa-5557e773bfc2/image.gif)

왜 이런 상황이 발생했을까요? Union은 이 타입도 되고 저 타입도 되네? 모든 타입을 사용하게 해주는 + 의 개념이구나!라고 생각한 부분에서 온 실수입니다. 제가 기대했던 결과를 가져오려면 **intersection type**을 써야 합니다.

`User | Account`라는 것은 `User` 타입을 사용했을 때도 `Account`라는 타입을 사용했을 때도 이 코드는 문제가 없어야 함을 뜻합니다. 아까의 코드는 `Account`라는 타입이 들어왔을 때 `name`에 접근할 수 없고, `User`라는 타입이 들어왔을 때는 `created_at`에 접근할 수 없게 돼요. 결론적으로 `UserAccount`는 그 어떠한 타입도 가지지 않게 된 것이죠.

이것이 union으로 interface를 다룰 때 주의해야 하는 점입니다. interface로 만든 A와 B타입에 union type을 사용하게 되면 A와 B에 공통적으로 들어가 있는 즉, **교집합**인 속성만 사용 가능합니다.

# 🧐 Intersection Type

위에서 제가 원하던 결과를 얻으려면 intersection type을 사용해야 한다고 언급했습니다. intersection은 무엇일까요? intersection은 제가 오해했던 union type의 개념에 가깝습니다. **Union은 교집합에 가깝고 intersection은 합집합에 가까워요.** 완전히 반대로 생각을 해왔던 거죠.

그리고 또 저는 여기서 삽질을 시작했습니다. 아 합집합이구나 그럼 이것도 가능하겠네!라고 생각했어요.

```javascript
interface A {
  name: string;
}

interface B {
  name: number;
}

type C = A & B;

const object: C = {
  name: "세리",
};
```

그러나 다음과 같은 오류가 발생합니다.

> Type 'string' is not assignable to type 'never'.

`name: string | number`을 기대했지만 결과는 그렇지 않았습니다. 이해가 안 돼서 다른 경우도 세워봤어요.

```javascript
type A = string;
type B = number;
type C = A & B;
```

C는 never 타입이 됩니다. 저는 `string | number`을 기대했는데 말이죠. 기대한 결과를 가져오려면 union 타입을 사용해야 합니다. `A & B` 라는 것은 A타입과 B타입을 둘 다 동시에 모두 가져야 한다라고 말하는 것과 같은데 그런 경우는 절대 없으니 never라는 타입이 됩니다.

```javascript
interface A {
  name: number;
  email: string;
}

interface B {
  name: string;
  age: number;
}

type C = A & B;
```

# 결론

타입을 오해하고 멋대로 단정 지으니 바보 같은 실수와 추측을 계속 이어가게 됐습니다. 다시 이런 오해를 하고 살지 말자고 미래의 저에게 부탁하기 위해 글을 썼습니다. 에러가 생기면 정확히 짚고 넘어가자.

<br>

**참고 자료**

[TypeScript 강의](https://www.udemy.com/course/best-typescript-21/)

[타입스크립트 핸드북](https://www.udemy.com/course/best-typescript-21/)
