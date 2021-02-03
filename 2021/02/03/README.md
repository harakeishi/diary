# Webページの表示
## webブラウザの仕組み
### トークン化アルゴリズム
- HTMLの文字列を分割するためのアルゴリズム
- [HTML Living Standard](https://html.spec.whatwg.org/multipage/parsing.html#tokenization)の13.2.5で決められている
![](https://cou929.nu/docs/how-browsers-work/_images/image019.png)
  
### ノードの構造
- DOMツリーはノードによって構成される
- ノードはNodeインタフェースを実装するオブジェクト
- このオブジェクトでは、インタフェースで定義されたデータに、アクセス・操作を行える

### ツリー構築アルゴリズム
- 「[HTMLLivingStandard](https://html.spec.whatwg.org/multipage/parsing.html#tree-construction)」の13.2.6で決められている
- 処理の流れ的には
  1. Documentオブジェクトのみのツリー生成＝＞initinal状態に移行
  2. HTMLトークンを受け取る＝＞befor html状態に移行
  3. HTMLトークンを受け取る＝＞エレメントを作成＋ツリーに追加
  4. TokenのTypeが変われば状態を移行
  5. 3と４をHTMLトークンがなくなるまで繰り返す

### 描画
ツリーのルートノードから処理をしていき、子ノードへと処理を引き継いでいく
