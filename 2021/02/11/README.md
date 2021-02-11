# Promiseと配列
`Promise.all` というものがある。
とりあえずthenで複数の非同期処理を実行する例を以下に示す。
```js
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
const request = {
    comment() {
        return fetchURL("https://azu.github.io/promises-book/json/comment.json").then(JSON.parse);
    },
    people() {
        return fetchURL("https://azu.github.io/promises-book/json/people.json").then(JSON.parse);
    }
};
function main() {
    function recordValue(results, value) {
        results.push(value);
        return results;
    }
    // [] は記録する初期値を部分適用している
    const pushValue = recordValue.bind(null, []);
    return request.comment()
        .then(pushValue)
        .then(request.people)
        .then(pushValue);
}



// 実行例
main().then((value) => {
    console.log(value);
}).catch((error) => {
    console.error(error);
});
```
上記のように結構くどい。
このような複数の非同期処理をまとめてすっきり扱う、 `Promis.all`と`Promise.race`という静的メソッドがある

# Promise.all
`Promise.all`はPromiseオブジェクトの配列を受け取り、その配列に入っているPromiseオブジェクトがすべてresolveされたときに、つぎの `.then`を呼び出す。

先ほどの処理を`Promise.all`で書くと以下のようになる。
```js
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
const request = {
    comment() {
        return fetchURL("https://azu.github.io/promises-book/json/comment.json").then(JSON.parse);
    },
    people() {
        return fetchURL("https://azu.github.io/promises-book/json/people.json").then(JSON.parse);
    }
};
function main() {
    return Promise.all([request.comment(), request.people()]);
}



// 実行例
main().then((value) => {
    console.log(value);
}).catch((error) => {
    console.error(error);
});
```

`Promise.all([request.comment(), request.people()])`でcomment()とpeopleは同時に実行されるが、それぞれの結果は`all`に渡した配列の順番で保存される。

このことは以下のコードを実行するとわかる。 

```js
// `delay`ミリ秒後にresolveする
function timerPromisefy(delay) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(delay);
        }, delay);
    });
}
const startDate = Date.now();
// 全てがresolveされたら終了
Promise.all([
    timerPromisefy(1),
    timerPromisefy(32),
    timerPromisefy(64),
    timerPromisefy(128)
]).then((values) => {
    console.log(Date.now() - startDate + "ms");// 約128ms
    console.log(values); // [1,32,64,128]
});
```
結果
> [object Promise]
129ms
[ 1, 32, 64, 128 ]

# Promise.race
`Promise.all` と同様に複数のpromiseオブジェクトを扱うもの。

使い方は`.all`と一緒だが、並列実行の非同期処理のうちどれか一つが終わればつぎの処理を実行する。
```js
// `delay`ミリ秒後にresolveする
function timerPromisefy(delay) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(delay);
        }, delay);
    });
}
// 一つでもresolve または reject した時点で終了
Promise.race([
    timerPromisefy(1),
    timerPromisefy(32),
    timerPromisefy(64),
    timerPromisefy(128)
]).then((value) => {
    console.log(value); // => 1
});
```
結果
> [object Promise]
1

各非同期処理自体は実行されるが、次の処理に値が引き渡されるのは`resolve`して戻ってきた最初の値のみ。

# then or catch?
`then(undefined, onRejected)`と`catch`はどっちをつかうのがいいのか？

以下のような例の１でthenを使ってエラーハンドリングしようとすると、正しくハンドリングできないので、catchを使おう
```js
function throwError(value) { // 例外を投げる
    throw new Error(value);
}
// <1> onRejectedが呼ばれることはない
function badMain(onRejected) {
    return Promise.resolve(42).then(throwError, onRejected);
}
// <2> onRejectedが例外発生時に呼ばれる
function goodMain(onRejected) {
    return Promise.resolve(42).then(throwError).catch(onRejected);
}



// 実行例
badMain(function(){
    console.log("BAD");
});
goodMain(function(){
    console.log("GOOD");
});
```
## まとめ
1. promise.then(onFulfilled, onRejected) において
  - onFulfilled で例外がおきても、この onRejected はキャッチできない
2. promise.then(onFulfilled).catch(onRejected) とした場合
  - then で発生した例外を .catch でキャッチできる
3. .thenと.catchに本質的な意味の違いはない
  - 使い分けると意図が明確になる
