# Promiseの書き方
https://azu.github.io/promises-book/#chapter2-how-to-write-promise
## Promise.resolve
`Promise.resolve(value)` という静的メソッドは、 `new Promise()` のショートカットとなるメソッド。
例）
`Promise.resolve(42);` は以下のコードのシンタックスシュガー
```js
new Promise((resolve) => {
  resolve(42);
});
```
resolveメソッドで作成したPromiseオブジェクトも `.then` を使った処理を書くことができる
```js
Promise.resolve(42).then((value) => {
  console.log(value);
 });
```

`Promise.resolve` の大きな特徴として、thenableなオブジェクトをPromiseオブジェクトに変換するという機能がある。
> thenableとは、Promiseっぽいオブジェクトのことをいう

通常の使用ではあんまり使う機会がないので、こういった機能があるんだということだけ覚えとけば良さそう。
### まとめ
`Promise.resolve` は、「渡した値でFulfilledされるPromiseオブジェクトを返すメソッド」という感じ
## Promise.reject
`Promise.reject(error)` は `Promise.resolve(value)` と同じ静的メソッドで、 `new Promise()` のショートカットとなるメソッド。
例）
`Promise.reject(new Error("エラー"))` は以下のコードのシンタックスシュガー
```js
new Promise((resolve, reject) => {
  reject(new Error("エラー"));
});
```
以下のようにcatchを使うことができる
```js
Promise.reject(new Error("エラー")).catch((error) => {
  console.log(error);
});
```
### まとめ
`Promise.reject` は、「渡した値でRejectedされるPromiseオブジェクトを返すメソッド」という感じ

> `Promise.resolve` と `Promise.reject` はPromiseオブジェクトがすぐ、 `resolve/reject` されるので、 `then/catch` に登録された関数もすぐに処理されるので、同期的かと錯覚してしまうけど、実際は非同期。

```js
const promise = new Promise((resolve) => {
    console.log("inner promise"); // 1
    resolve(42);
});
promise.then((value) => {
    console.log(value); // 3
});
console.log("outer promise"); // 2

// 実行結果
inner promise // 1
outer promise // 2
42            // 3
```
なぜ同期的に実行できるのに非同期で実行するのか？

配置する場所によって、 コンソールに出てくるメッセージの順番が変わってしまうコードがあるため。
つまり配置する場所によって結果が変わってしまうため、あえて非同期でしている。
この問題は、 「Effective JavaScript」 の 「項目67 非同期コールバックを同期的に呼び出してはいけない 」で以下のように記載されている
・非同期コールバックは（たとえデータが即座に利用できても）決して同期的に使ってはならない。
・非同期コールバックを同期的に呼び出すと、処理の期待されたシーケンスが乱され、 コードの実行順序に予期しない変動が生じるかもしれない。
・非同期コールバックを同期的に呼び出すと、スタックオーバーフローや例外処理の間違いが発生するかもしれない。
・非同期コールバックを次回に実行されるようスケジューリングするには、setTimeout のような非同期APIを使う。

以上のことから、 ***Promiseは常に非同期で処理される***ということが仕様で定められている。
