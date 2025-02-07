# react-hooks-fetch

[![CI](https://img.shields.io/github/workflow/status/dai-shi/react-hooks-fetch/CI)](https://github.com/dai-shi/react-hooks-fetch/actions?query=workflow%3ACI)
[![npm](https://img.shields.io/npm/v/react-hooks-fetch)](https://www.npmjs.com/package/react-hooks-fetch)
[![size](https://img.shields.io/bundlephobia/minzip/react-hooks-fetch)](https://bundlephobia.com/result?p=react-hooks-fetch)
[![discord](https://img.shields.io/discord/627656437971288081)](https://discord.gg/MrQdmzd)

React Supense와 사용하는 최소의 데이터 패치(Fetch) 라이브러리.

## 소개

이 라이브러리는 리액트 훅인 비동기 함수들을 위한 `useFetch`를 제공합니다.
해당 함수는 React Suspense를 활용하고, 사전에 `createFetch`를 통한 스토어 생성이 요구됩니다.
해당 API는 랜더링이 되기 전에 데이터를 강제로 가져오록 설계되있습니다.

프로젝트 현황: 실험단계이며, 피드백이 필요합니다.

## 설치

```bash
npm install react-hooks-fetch
```

## 사용방법

```javascript
import React, { Suspense } from "react";
import { ErrorBoundary } from "react-error-boundary";

import { createFetch, useFetch } from "react-hooks-fetch";

// 1️⃣
// 비동기 함수와 함께 스토어를 생성합니다.
// 비동기 함수는 한개의 입력 인자를 가져올 수 있습니다.
// 그 입력 값은 캐시에 대한 "키" 값이 됩니다.
// 기본적으로, 키들은 일치 연산자(`===`)를 통해 비교되어 집니다.
const store = createFetch(async (userId) => {
  const res = await fetch(`https://reqres.in/api/users/${userId}?delay=3`);
  const data = await res.json();
  return data;
});

// 2️⃣
// 랜더링되기 전에 어디에서든 데이터를 가져와야 합니다.
// 만약 초기 랜더링때, `undefined` 값을 허용한다면,
// 이 과정은 생략가능 합니다.
store.prefetch("1");

// 3️⃣
// 스토어를 사용할 컴포넌트를 정의하세요.
// `refetch` 함수는 그 입력 인자를 사용합니다.
// 그리고 랜더링 되기 전 데이터를 가져옵니다.
const Main = () => {
  const { result, refetch } = useFetch(store, "1");
  const handleClick = () => {
    refetch("2");
  };
  return (
    <div>
      <div>First Name: {result.data.first_name}</div>
      <button type="button" onClick={handleClick}>
        Fetch user 2
      </button>
    </div>
  );
};

// 4️⃣
// 외부 컴포넌트로 ErrorBoundary와 Suspense가 있어야 합니다.
const App = () => (
  <ErrorBoundary fallback={<h1>Error</h1>}>
    <Suspense fallback={<span>Loading...</span>}>
      <Main />
    </Suspense>
  </ErrorBoundary>
);
```

## API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### createFetch

fetch store를 생성합니다.

#### Parameters

- `fetchFunc` **FetchFunc\<Input, Result>**
- `options` **Options\<Input, Result>?**

#### 예제

```javascript
import { createFetch } from 'react-hooks-fetch';

const fetchFunc = async (userId) => {
  const res = await fetch(`https://reqres.in/api/users/${userId}?delay=3`));
  const data = await res.json();
  return data;
};
const store = createFetch(fetchFunc);
store.prefetch('1');
```

### useFetch

useFetch hook

#### Parameters

- `store` **FetchStore\<Input, Result>**
- `initialInput` **Input?**

#### Examples

```javascript
import { useFetch } from "react-hooks-fetch";

const { result, refetch } = useFetch(store, initialInput);
```

## 예제들

[예제](examples) 폴더는 실행되는 예제들이 포함되어 있습니다.
에제들은 다음 아래와 같은 명령어와 실행 가능하고,

```bash
PORT=8080 npm run examples:01_minimal
```

그리고 웹 브라우저에서 <http://localhost:8080>로 열 수 있습니다.

또한 codesandbox.io에서 예제들을 확인 할 수 있습니다:
[01](https://codesandbox.io/s/github/dai-shi/react-hooks-fetch/tree/main/examples/01_minimal)
[02](https://codesandbox.io/s/github/dai-shi/react-hooks-fetch/tree/main/examples/02_typescript)
[03](https://codesandbox.io/s/github/dai-shi/react-hooks-fetch/tree/main/examples/03_noinit)

## Blogs

이전 구현에 대한 [히스토리](./HISTORY.md)에서 볼수 있습니다.

- [React Hooks Tutorial on Developing a Custom Hook for Data Fetching](https://blog.axlight.com/posts/react-hooks-tutorial-on-developing-a-custom-hook-for-data-fetching/)
- [useFetch: React custom hook for Fetch API with Suspense and Concurrent Mode in Mind](https://blog.axlight.com/posts/usefetch-react-custom-hook-for-fetch-api-with-suspense-and-concurrent-mode-in-mind/)
- [Developing a React Library for Suspense for Data Fetching in Concurrent Mode](https://blog.axlight.com/posts/developing-a-react-library-for-suspense-for-data-fetching-in-concurrent-mode/)
- [Diving Into React Suspense Render-as-You-Fetch for REST APIs](https://blog.axlight.com/posts/diving-into-react-suspense-render-as-you-fetch-for-rest-apis/)
