# Redux saga
`yarn create react-app redux-saga --template redux-typescript`
`yarn add redux-saga`

```js
/*file store.ts*/
// import
import createSagaMiddleware from 'redux-saga';
import rootSaga from './rootSaga';

// khởi tạo
const sagaMiddleware = createSagaMiddleware()

// thêm sagaMiddleware vào middleware default
export const store = configureStore({
    reducer: {},
    middleware: (getDefaultMiddleware) => getDefaultMiddleware().concat(sagaMiddleware)
});

// run saga middleware
sagaMiddleware.run(rootSaga);
```

```js
/*file rootSaga.ts*/
// library
import { all } from 'redux-saga/effects';
// files
import counterSaga from 'features/counter/counterSaga';

// function*  === Generator function
function* helloSaga() {
    console.log('helloSaga')
}
// chạy rootSaga song mới chạy các saga con trong yield all([])
export default function* rootSaga() {
    console.log('rootSaga')
    yield all([ helloSaga(),counterSaga()])
}
```

```js
/*file counterSaga.ts*/
import { PayloadAction } from '@reduxjs/toolkit';
import { takeEvery } from 'redux-saga/effects';
import { decrement } from './counterSlice';

function* log(action: PayloadAction) {
    console.log('Log', action);
}
export default function* counterSaga() {
    // console.log chay 1 lan
    console.log('counterSaga')  
    // yield chay nhieu lan
    // tất cả action đc dispatch lên thì gọi log ra 
    yield takeEvery('*', log)
    // action  đc dispatch lên thì gọi log ra 
    yield takeEvery('counter/decrement', log)
    yield takeEvery(decrement().type, log)
    yield takeEvery(decrement.toString(), log)
}
```


## yield
```js
import { delay, put, takeEvery, takeLatest } from 'redux-saga/effects';

    // đợi 2s rồi mới chạy tiếp vs delay
    yield delay(1000)
    
    // dispatch action vs = put
    yield put(incrementSagaSuccess(action.payload));
    
    // chạy khi có dispatch chỉ định
    yield take(authActions.login.type)
    
    // chạy mỗi khi có dispatch
    yield takeEvery(incrementSaga.toString(), handleIncrementSaga)
    
    // nếu dispatch liên tục mà thằg trước chưa chạy xong thì hủy thằng trước 
    // => chỉ chạy thằng dispatch mới nhất 
    yield takeLatest(incrementSaga.toString(), handleIncrementSaga)
    
    // fork dùng để gọi hàm , cho phép thằng đứng sau chạy kể cả khi nó chưa xong
    yield fork(handleLogout)
    
    // call dùng để gọi hàm , thằng đứng sau phải đợi nó chạy xong
    yield call(handleLogout)
```