# promise#then
Promiseではいくらでもメソッドチェーンをつなげて処理をかける。
つぎのようなメソッドチェーン

```js
function taskA() {
    console.log("Task A");
}
function taskB() {
    console.log("Task B");
}
function onRejected(error) {
    console.log("Catch Error: A or B", error);
}
function finalTask() {
    console.log("Final Task");
}

const promise = Promise.resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
```
![](https://azu.github.io/promises-book/Ch2_HowToWrite/img/promise-then-catch-flow.png)

### promise chainでの値渡し
メソッドチェーンで値渡しする場合は前のチェーンでreturnすればOK

```js
function doubleUp(value) {
    return value * 2;
}
function increment(value) {
    return value + 1;
}
function output(value) {
    console.log(value);// => (1 + 1) * 2
}

const promise = Promise.resolve(1);
promise
    .then(increment)
    .then(doubleUp)
    .then(output)
    .catch((error) => {
        // promise chain中にエラーが発生した場合に呼ばれる
        console.error(error);
    });
```
このreturnする値は、数値や文字列だけではなく、オブジェクトやPromiseオブジェクトもReturnできる。
Returnした値は、 `Promise.resolve(returnされた値）`のように処理されるので、なにをReturnしても最終的には新しいPromiseオブジェクトが返される。
### まとめ
Promise.thenは、単にコールバック関数をとうろくするだけではなく、受け取った値いを変換させてべつのPromiseオブジェクトを生成するという機能も持っている。

# promise#catch
`Promise.catch` は　`promise.then(undefined, onrejected)` のエイリアスとなるメソッド。

IE8以下はECMAScript 3の実装なので、catchが使えない（予約語）

# promise#finally
ECMASScript 2018からPromise Chainの最後に処理を実行する `Promise#finally` メソッドが追加された。

`Promise#finally` メソッドは成功失敗にかかわらず呼び出すコールバック関数を登録する。

`try...catch...finally` と同様の役割を持つメソッド。
```js
Promise.resolve("成功").finally(() => {
    console.log("成功時に実行される");
});
Promise.reject(new Error("失敗")).finally(() => {
    console.log("失敗時に実行される");
});
```
`finally` メソッドのコールバック関数は引数を受け取らず、どのような値いをreturnしてもPromise Chainには影響を与えない。

また、`finally`メソッドは新しいPromiseオブジェクトを返し、新しいPromiseおぶじぇくとは呼び出し元のPromiseオブジェクトをの内容をそのまま返す。
```js
function onFinally() {
    // 成功、失敗どちらでも実行したい処理
}

// `Promise#finally` は新しいpromiseオブジェクトを返す
Promise.resolve(42)
    .finally(onFinally)
    .then((value) => {
        // 呼び出し元のpromiseオブジェクトの状態をそのまま引き継ぐ
        // 呼び出し元のpromiseオブジェクトは `42` で resolveされている
        console.log(value); // 42
    });
```
これの使い時は、APIでデータを取得中のフラグを成功失敗にかかわらず、終わったらfalseにする場合など。
