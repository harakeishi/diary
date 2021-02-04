# ボヤッと使っていたPromiseをしっかり理解する
今日から [JavaScript Promise](https://azu.github.io/promises-book/)を読んでいく！！

## 1.1 Promiseとは何か？
Promiseは非同期処理を抽象化したオブジェクトとそれを操作する仕組みのこと。
JavaScriptにおける非同期処理といえば、コールバックを利用する場合が多いが、ルールが統一されていないため、書き方がいろいろある。
そこで、統一的なインターフェースで書くように非同期に対するオブジェクトとルールを仕様化したのがPromise。
```js
//コールバックを使った非同期処理の一例
getAsync("fileA.txt", (error, result) => { 
    if (error) { // 取得失敗時の処理
        throw error;
    }
    // 取得成功の処理
});

//Promiseを使った非同期処理の一例
const promise = getAsyncPromise("fileA.txt"); 
promise.then((result) => {
    // 取得成功の処理
}).catch((error) => {
    // 取得失敗時の処理
});
```
非同期処理を抽象化したpromiseオブジェクトというものを用意し、 そのpromiseオブジェクトに対して成功時の処理と失敗時の処理の関数を登録するようにして使う。
コールバック関数と比べると何が違うのかは、 非同期処理の書き方がpromiseオブジェクトのインターフェースに沿った書き方に限定されている点。
つまり、promiseオブジェクトに用意されているメソッド(ここでは then や catch)以外は使えないため、 コールバックのように引数に何を入れるかが自由に決められるわけではなく、一定のやり方に統一される。
### Promiseの役割まとめ
- 非同期処理において統一されたインターフェースを提供し、非同期処理をパターン化しやすくする

---
## 1.2 Promiseの概要
PromiseのAPIは大きく分けて３つ
- Constructor
  - コンストラクタ関数である `Promise` からインスタンスとなる `Promise` オブジェクトを作成する。
  ```js
  const promise = new Promise((resolve, reject) => {
      // 非同期の処理
      // 処理が終わったら、resolve または rejectを呼ぶ
  });
  ```
- Instance Method
  - `.then()` や `.catch()` などのこと
  - resolve(成功) / reject(失敗) した時に呼ばれる コールバック関数を登録する役割がある
- Static Method
  - `Promise.all()` や `Promise.resolve()` などPromiseを扱う上での補助メソッドなど

```js
// Promiseの書き方例
function asyncFunction() {
    
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve("Async Hello world");
        }, 16);
    });
}

asyncFunction().then((value) => {
    console.log(value); // => 'Async Hello world'
}).catch((error) => {
    console.error(error);
});
```

catchを使わずに書く場合
```js
asyncFunction().then((value) => {
    console.log(value);
}, (error) => {
    console.error(error);
});
```

### Promiseの状態
`new Promise` でインスタンス化したpromiseオブジェクトには３つの状態がある。
- Fulfilled
  - 成功(resolve)した状態：この状態になった時, `onFulfilled` が呼ばれる。
- Rejected
  - 失敗(reject)した状態：この状態になった時, `onRejected` が呼ばれる。
- Pending
  - 上記２つ以外の場合→オブジェクト作成から他の状態にうつるまではこの状態ということ
  
  ![](https://azu.github.io/promises-book/Ch1_WhatsPromises/img/promise-states.png)
  
ここからわかることは、 `pendding` から他の状態になった後、Promiseオブジェクトの状態はそれ以上変化しないということ。
つまり、 `.then()` などで登録したコールバック関数が呼ばれるのは１度だけ
このようなことから、FulfilledとRejectedのどちらかの状態であることをSettled(不変の)と表現することがある。

## 1.3 Promiseの書き方
### Promiseオブジェクトの作成
作成の流れは以下のよう
1. `new Promise(function)` でPromiseオブジェクトをインスタンス化
2. functionでは非同期などの処理をかく
  - 処理結果が正常ならresolve(結果の値) を呼ぶ
  - 処理結果がエラーなら、reject(Errorオブジェクト) を呼ぶ


```js
// 作成の例
function fetchURL(URL) {
    return new Promise((resolve, reject) => {
        const req = new XMLHttpRequest();
        req.open("GET", URL, true);
        req.onload = () => {
            if (200 <= req.status && req.status < 300) {
                resolve(req.responseText);
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = () => {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}

// 実行例
const URL = "https://httpbin.org/get";
fetchURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});
```
