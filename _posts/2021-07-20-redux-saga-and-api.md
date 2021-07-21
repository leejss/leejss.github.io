---
layout: post
title: redux-toolkit, redux-saga로 api 요청하기 (with Typescript)
date: 2021-07-20 21:47 +0900
tags:
  - typescript
  - redux
  - redux-saga
---

## Redux와 API 요청

컴포넌트에서 화면에 보여줄 데이터를 API요청을 통해 받아와야 하는 경우가 있다. 컴포넌트별 `state`와 `fetch` 등을 통해서 API 요청을 할 수도 있다.

```tsx
useEffect(() => {
  // API 요청
}, []);
```

하지만 컴포넌트 별로 데이터를 요청하는 코드를 작성하면 컴포넌트가 복잡해지고 재사용성이 떨어지길 마련이다. Redux를 통해서 API 호출을 모듈화할 수 있다. 그리고 컴포넌트에서는 다음과 같이 `dispatch` 하면 된다.

```tsx
useEffect(() => {
  // getProducts()는 Action creator
  dispatch(getProducts());
}, []);
```

redux에서 async 요청을 할 수 있도록 도와주는 라이브러리에는 대표적으로 redux-thunk, redux-saga 등이 있다. 그 중에서도 redux-toolkit과 redux-saga를 이용하여 API 호출을 하는 모듈을 작성하고자 한다.

### dependencies 설치하기

```sh
yarn add @reduxjs/toolkit react-redux redux redux-saga axios
```

### Folder strucure

redux-toolkit에서는 다음과 같은 폴더 구조를 사용하고 있다.

```txt
src
    app
        store.ts
    features
        products
            productsSlice.ts
            ...
        ...
```

redux store를 구성하는 코드는 `store.ts`에, reducer와 action에 관한 코드는 `slice`에 정의한다. 그리고 saga에 관련된 코드는 `productsSaga.ts`처럼 파일을 하나 더 만들어 모듈화 한다.

### Store 구성

redux를 리액트 컴포넌트에서 사용하기 위해서는 다음과 같이 제공해야 한다.

```tsx
import store from "./app/store";

<Provider store={store}>
  <App />
</Provider>;
```

`store.ts`은 다음과 같은 구조로 이루어져 있다.

```ts

function createStore() {
    // 여기서 store를 생성한다.
    const store = configureStore({
        reducer: {
            // 여기서 reducer를 mapping한다.
        }
        middleware: [/*미들웨어를 넣어준다. sagaMiddleware가여기 들어간다.*/]
    })

    return store
}

const store = createStore()

// RootState type. useSelector 사용 시 state 타입을 위해 필요하다.
export type RootState = ReturnType<typeof store.getState>

export default store
```

여기에 `redux-saga` 미들웨어를 추가해줘야 한다. `saga` 미들웨어는 `rootSaga`로 통합한다.

```ts
import createSagaMiddleware from "redux-saga";

const sagaMiddleware = createSagaMiddleware();

function* rootSaga() {
  yield all([call(/*여기에 개별 saga가 들어간다*/)]);
}

function createStore() {
  const store = configureStore({
    // ...
    middleware: [sagaMiddleware],
  });
  sagaMiddleware.run(rootSaga);

  return store;
}
```

이렇게 작성하면 redux와 redux-saga 셋팅이 완료되었다.

### Slice 생성

slice는 `redux-toolkit`에서 제안하는 `redux` 코드 작성 방식인듯 하다.
`redux-toolkit`은 `createSlice()`라는 함수를 제공한다. 이 함수를 통해서 Action, Action creator와 reducer를 한꺼번에 만들 수 있다.

slice를 만들기 전에 state 타입과 initialState를 정의한다.

```ts
// types.ts
export interface Product {
  id: number;
  title: string;
  price: number;
  description: string;
  category: string;
  image: string;
}
```

```ts
// productsSlice.ts
interface ProductsState {
  loading: boolean;
  data: Product[] | null;
  erorr: AxiosErorr | null;
}

const initialState: ProductsState = {
    loading: false,
    data: null
    error: null
};
```

slice를 작성한다.

```ts
const productsSlice = createSlice({
  name: "products",
  initialState,
  reducers: {
    // 액션에 따른 reducer 로직을 작성한다.
    // createSlice가 자동으로 state의 타입을 추론한다.
    // 또한 immer를 사용하고 있어 함수 몸체 안에서 직접 변경해도 불변성을 유지한다.
    getProducts: (state) => {
      // loading => true
      state.loading = true;
    },
    getProductsSuccess: (state, { payload }) => {
      // API 요청이 성공적이면 데이터를 payload에 들어온다.
      state.data = payload;
      state.loading = false;
    },
    getProductsError: (state, { payload }) => {
      state.error = payload;
      state.loading = false;
    },
  },
});

// 정의한 액션과 리듀서를 export한다.
export const productsActions = productsSlice.actions;
export default productsSlice.reducer;
```

### Saga 생성

액션과 리듀서를 정의했으면 실제 API 호출을 하는 saga를 작성해야 한다. saga는 Effect description이라고 하는 자바스크립트 객체를 generate하는 Generator함수를 말한다.

```tsx
// productsSaga.ts
import { productsActions } from "./productsSlice";
import * as productsAPI from "../api/productsAPI";
import { call, put, takeLatest } from "redux-saga/effects";
function* getProducts() {
  // yield과정에서 발생하는 error는 catch에서 걸린다.
  try {
    // API 요청을 한다.
    // call(fn)에서 만약 fn이 Promise를 반환한다면 resovle될 때 까지 기다리고 결과를 generate한다.
    // 따라서 response에는 API 요청 응답이 담기게 된다.
    // axios를 통해 요청하기 때문에 AxiosResponse로 타입을 명시해준다.
    const response: AxiosResponse = yield call(productsAPI.getProducts);
    // put(action)은 action을 dispatch를 한다.
    yield put(productsActions.getProductsSuccess(response.data));
  } catch (error) {
    // 위 과정에서 에러가 발생하면 여기서 다룬다.
    yield put(productsActions.getProductsError(error));
  }
}

// 그 다음 getProducts 액션을 감지하는 saga를 작성한다.
function* watchGetProducts() {
  // 만약 getProducts액션 (패턴이라고도 하는데)이 감지되면, getProductsSaga를 호출한다.
  yield takeLatest(productsActions.getProducts, getProducts);
}

// watchGetProducts를 바로 export 해서 rootSaga에 넣어도 되는데 saga가 여러개 인 경우 saga로 한번더 감싸준다.
export default function* getProductsSaga() {
  yield all([fork(watchGetProducts)]);
}
```

### RootSaga

방금 정의한 saga를 rootSaga에 포함시켜준다.

```ts
// store.ts

function* rootSaga() {
  yield all(call(getProductsSaga));
}
```

여기까지가 API를 호출하는 saga를 작성하고 rootSaga에 포함시키는 과정이다.

### dispatch

API 호출 로직을 담당하는 saga를 작성했으니 사용할 차례다. 컴포넌트 마운트 시, 비동기 요청은 `useEffect()` 안에 넣어준다.

```ts
// ProductsContainer.tsx
import { useDispatch } from "react-redux";
import { productsActions } from "../features/products/productsSlice";

const ProductsContainer = () => {
  const dispatch = useDispatch();
  useEffect(() => {
    dispatch(productsActions.getProducts());
  }, []);
  return (
    // ...
  )
};
```
