---
layout: post
title: React-Express Local dev 환경설정
date: 2021-04-29 17:46 +0900
tags: React Express
---

## react와 express 사용

프론트 엔드에서 react를 사용하고 백엔드에서 express를 사용하여 로컬 개발환경을 구성할 수 있다.

이렇게 하면 직접 API를 구성하거나 데이터베이스를 통하여 다채로운 개발경험을 할 수 있다.

## Localhost

CRA로 생성한 react app은 localhost의 3000번 포트를 사용한다.

express 서버가 예를 들어 localhost의 8000번 포트를 사용하고 react app에서 api를 요청한다면 CORS 에러가 발생한다.

두 가지 방법으로 해결할 수 있다.

## cors 패키지

expess app의 미들웨어로 `cors`를 등록한다.

```bash
yarn add cors
```

```js
import cors from 'cors'
// ...
app.use(cors())
```

```js
// React component
const App = () => {
  const [msg, setMsg] = useState("");
  useEffect(() => {
    async function fetchMsg() {
      const response = await axios.get("http://localhost:8000/test");
      const data = response.data;
      setMsg(data.msg);
    }
    fetchMsg();
  });
  return <h1>{msg}</h1>;
};
```

## proxy 이용

[Proxying API Requests in Development](https://create-react-app.dev/docs/proxying-api-requests-in-development/){:target="_blank"}

CRA로 만든 react app은 proxy 서버를 제공한다.

proxy 서버를 셋팅하는 방법은 간단하다.

react 앱 루트 폴더 내 `package.json`에

```json
"proxy": "http://localhost:8000"
```

을 추가하면 된다.

대신 클라이언트에서 API를 요청할 때, `http://localhost:8000/test`가 아니라 `/test` 이런 식으로 요청해야 한다.
