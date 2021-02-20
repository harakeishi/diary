# Async Function

Async Functionとは、非同期処理を行う関数を定義する構文。

必ず `Promise` インスタンスを返す。

この構文のいいところは、非同期処理をあたかも同期処理のようにかけること。

```js
async function doAsync() {
  return "value";
}

doAsync().then((value) => {
  console.log(value);
});
```
これは、以下の内容と同じことをしている。
```js
function doAsync() {
  return Promise.resolve("Value");
}

doAsync().then((value) => {
  console.log(value);
});
```

Async FunctionにはPromiseの非同期処理が完了するまで待つ、 `await` というものが使える。
以下のような処理を
```js
function fetchBookTitle() {
    // Fetch APIは指定URLのリソースを取得しPromiseを返す関数
    return fetch("https://azu.github.io/promises-book/json/book.json").then((res) => {
        return res.json(); // レスポンスをJSON形式としてパースする
    }).then((json) => {
        return json.title; // JSONからtitleプロパティを取り出す
    });
}

function main() {
    // `fetchBookTitle`関数は、取得したJSONの`title`プロパティでresolveされる
    fetchBookTitle().then((title) => {
        console.log(title); // => "JavaScript Promiseの本"
    });
}

main();
```

Async/awaitで書き換えると、
```js
// `async`をつけて`fetchBookTitle`関数をAsync Functionとして定義
async function fetchBookTitle() {
    // リクエストしてリソースを取得する
    const res = await fetch("https://azu.github.io/promises-book/json/book.json");
    // レスポンスをJSON形式としてパースする
    const json = await res.json();
    // JSONからtitleプロパティを取り出す
    return json.title;
}

// `async`をつけて`main`関数をAsync Functionとして定義
async function main() {
    // `await`式で`fetchBookTitle`の非同期処理が完了するまで待つ
    // `fetchBookTitle`がresolveした値が返り値になる
    const title = await fetchBookTitle();
    console.log(title); // => "JavaScript Promiseの本"
}

main();
```
とかける。

## Asyncの構文
### 宣言の仕方
```js
// 関数宣言のAsync Function版
async function fn1() {}
// 関数式のAsync Function版
const fn2 = async function() {};
// Arrow FunctionのAsync Function版
const fn3 = async() => {};
// メソッドの短縮記法のAsync Function版
const object = {
    async method() {}
};
```
動作
- Async FunctionはPromise以外の値をreturnした場合、その返り値をもつFulfilledなPromiseを返す
- Async FunctionがPromiseをreturnした場合、その返り値のPromiseをそのまま返す
- Async Function内で例外が発生した場合は、そのエラーをもつRejectedなPromiseを返す
## awaitをkwsk
awaitはAsync Function内のみで使用可能

await式は右辺のPromiseインスタンスがFulfilledかRejectedになるまで、その行で非同期処理の完了を待ち、終わり次第次の行の処理を実行する。
```js
// async functionは必ずPromiseを返す
async function doAsync() {
    // 非同期処理
}
async function asyncMain() {
    // doAsyncの非同期処理が完了するまでまつ
    await doAsync();
    // 次の行はdoAsyncの非同期処理が完了されるまで実行されない
    console.log("この行は非同期処理が完了後に実行される");
}
```
await式はエラーをthrowする。

つまり、以下のように `try...catch` 構文が使える。
```js
async function asyncMain() {
    // await式のエラーはtry...catchできる
    try {
        // `await`式で評価した右辺のPromiseがRejectedとなったため、例外がthrowされる
        const value = await Promise.reject(new Error("エラーメッセージ"));
        // await式で例外が発生したため、この行は実行されません
    } catch (error) {
        console.log(error.message); // => "エラーメッセージ"
    }
}
asyncMain().then(() => {
    console.log("この行は実行されます");
}, (error) => {
    // すでにtry...catchされているため、この行は実行されません
});
```

# 配列とAsync Function

# 読んだもの
https://azu.github.io/promises-book/#chapter5-async-function
