---
title: EJSを使用してノードアプリケーションをテンプレート化する方法
description: ノードアプリケーションを即急に作成する際、アプリケーションを手軽にテンプレート化する必要が時としてあります。これまでは、テンプレートなしでアプリケーションを構築してきました。
original_id: 6453

---

### はじめに

ノードアプリケーションを即急に作成する際、アプリケーションを手軽にテンプレート化する必要が時としてあります。

[Jade](http://jade-lang.com/)は、Expressがデフォルトとして使用するビューエンジンですが、Jade構文は多くのユースケースにとってあまりに複雑です。[EJS](https://ejs.co/)はその代用としてよく機能し、セットアップが非常に簡単です。簡単なアプリケーションを作成し、EJSを使用して**サイトの繰り返し部分（パーシャル）をインクルード**し、**データをビューに渡す**方法を見てみましょう。

## デモアプリのセットアップ

アプリケーション用に2ページ作成します。1ページは**full width**、もう1ページは**sidebar**にします。

<$>[note]**コードを取得:**[GitHubに、完全なデモコードのgit repoがあります。](https://github.com/do-community/ejs-demo)<$>

### ファイル構造

アプリケーションに必要なファイルは以下のとおりです。viewsフォルダ内でテンプレートを作成します。その他はごく標準的なノードのフォルダです。

```
- views
----- partials
---------- footer.ejs
---------- head.ejs
---------- header.ejs
----- pages
---------- index.ejs
---------- about.ejs
- package.json
- server.js
```

`package.json`にはノードアプリケーション情報と必要な依存関係（[express](http://expressjs.com/)と[EJS](https://ejs.co/)）が、 `server.js`にはExpressサーバーのセットアップと設定が保持されています。ここでは、ページへのルートを定義します。

### ノードのセットアップ

`package.json`ファイルを見て、プロジェクトをセットアップしましょう。

```json
[label package.json]
{
  "name": "node-ejs",
  "main": "server.js",
  "dependencies": {
    "ejs": "^3.1.5",
    "express": "^4.17.1"
  }
}
```

必要なのはExpressとEJSだけです。ここで、定義したばかりの依存関係をインストールします。先に進み実行します。

```bash,custom_prefix($)
[environment local]
npm install
```

すべての依存関係がインストールされたので、EJSを使用するようにアプリケーションを設定し、必要な2ページ、つまりindexページ（full width）とaboutページ（sidebar）へのルートをセットアップします。これらをすべて`server.js`ファイルで行います。

```javascript
[label server.js]
// load the things we need
var express = require('express');
var app = express();

// set the view engine to ejs
<^>app.set('view engine', 'ejs');<^>

// use res.render to load up an ejs view file

// index page
app.get('/', function(req, res) {
    res.render('pages/index');
});

// about page
app.get('/about', function(req, res) {
    res.render('pages/about');
});

app.listen(8080);
console.log('8080 is the magic port');
```

ここではアプリケーションを定義し、ポート8080で表示するように設定します。また、`app.set('view engine', 'ejs');`を使用して、EJSをExpressアプリケーションのビューエンジンとしてセットアップします。`res.render()`をどのように使用してビューをユーザーに送信するかに注目してください。注目すべき点として、**ビューを求めてres.render()はviewsフォ**ルダを検索します。したがって、フルパスが`views/pages/index`なので、`pages/index`とだけ定義します。

### サーバー起動

先に進み、次を入力してサーバーを起動します。

```bash,custom_prefix($)
[environment local]
node server.js
```

これで、アプリケーションがブラウザ上の`http://localhost:8080`と`http://localhost:8080/about`で表示されます。アプリケーションがセットアップされたので、ビューファイルを定義し、EJSがどのように機能するか確認します。

## EJSパーシャルの作成

多くのアプリケーションと同様に、構築時に再利用されるコードがたくさんあります。これらの**コードをパーシャル**と呼び、サイト全体で使用する3ファイル、`head.ejs`、`header.ejs`、`footer.ejs`を定義します。早速これらのファイルを作成しましょう。
```ejs
[label views/partials/head.ejs]
<meta charset="UTF-8">
<title>EJS Is Fun</title>

<!-- CSS (load bootstrap from a CDN) -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.5.2/css/bootstrap.min.css">
<style>
    body { padding-top:50px; }
</style>
```

```ejs
[label views/partials/header.ejs]
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="/">EJS Is Fun</a>
  <ul class="navbar-nav mr-auto">
    <li class="nav-item">
      <a class="nav-link" href="/">Home</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="/about">About</a>
    </li>
  </ul>
</nav>
```
```ejs
[label views/partials/footer.ejs]
<p class="text-center text-muted">© Copyright 2020 The Awesome People</p>
```

## EJSパーシャルをビューに追加する

ここにパーシャルが定義されました。あとはビューに追加するだけです。`index.ejs`と`about.ejs`に移動し、`include`構文を使用してパーシャルを追加します。

### EJSパーシャルをインクルードする構文
EJSパーシャルを別のファイルに埋め込むには`<%<^>-<^> include(<^>'RELATIVE/PATH/TO/FILE'<^>) %>`を使用します。
- 単に`<%`ではなくハイフンを付けて`<^><%-<^>`とするのは、EJSに**生のHTML**をレンダリングするように指示するためです。
- パーシャルへのパスは、現在のファイルからの**相対**パスで示します。

```ejs
[label views/pages/index.ejs ]
<!DOCTYPE html>
<html lang="en">
<head>
    <^><%- include('../partials/head'); %><^>
</head>
<body class="container">

<header>
    <^><%- include('../partials/header'); %><^>
</header>

<main>
    <div class="jumbotron">
        <h1>This is great</h1>
        <p>Welcome to templating using EJS</p>
    </div>
</main>

<footer>
    <^><%- include('../partials/footer'); %><^>
</footer>

</body>
</html>
```

これで、定義されたビューをブラウザの`http://localhost:8080`で見ることができます。![node-ejs-templating-index](https://assets.digitalocean.com/articles/use-ejs-to-template-node-application/ejs-index.png)

aboutページにはブートストラップサイドバーも追加し、パーシャルがどのように構築され、さまざまなテンプレートやページで再利用されるのかを見ていきます。　

```ejs
[label views/pages/about.ejs ]
<!DOCTYPE html>
<html lang="en">
<head>
    <^><%- include('../partials/head'); %><^>
</head>
<body class="container">

<header>
    <^><%- include('../partials/header'); %><^>
</header>

<main>
<div class="row">
    <div class="col-sm-8">
        <div class="jumbotron">
            <h1>This is great</h1>
            <p>Welcome to templating using EJS</p>
        </div>
    </div>

    <div class="col-sm-4">
        <div class="well">
            <h3>Look I'm A Sidebar!</h3>
        </div>
    </div>

</div>
</main>

<footer>
    <^><%- include('../partials/footer'); %><^>
</footer>

</body>
</html>
```

`http://localhost:8080/about`を表示すると、サイドバー付きのaboutページが確認できます。![node-ejs-templating-about](https://assets.digitalocean.com/articles/use-ejs-to-template-node-application/ejs-about.png)

これで、EJSを使用してノードアプリケーションからビューにデータを渡せます。

## ビューとパーシャルにデータを渡す

基本的な変数と一覧を定義してホームページに渡しましょう。`server.js`ファイルに戻り、`app.get('/')`ルート内に次を追加します。

```javascript
[label server.js]
// index page
app.get('/', function(req, res) {
    var mascots = [
        { name: 'Sammy', organization: "DigitalOcean", birth_year: 2012},
        { name: 'Tux', organization: "Linux", birth_year: 1996},
        { name: 'Moby Dock', organization: "Docker", birth_year: 2013}
    ];
    var tagline = "No programming concept is complete without a cute animal mascot.";

    res.render('pages/index', {
        mascots: mascots,
        tagline: tagline
    });
});
```

`mascot`という一覧と`tagline`という簡単な文字列を作成しました。`index.ejs`ファイルに移動してこれらを使ってみましょう。

### EJSで単一の変数をレンダリング

変数を1つだけechoするために使うのは、`<%= tagline %>`だけです。これをindex.ejsファイルに追加しましょう。
```ejs
[label views/pages/index.ejs]
...
<h2>Variable</h2>
<p><%= tagline %></p>
...
```

### EJSでデータをループさせる

データをループさせるには、`.forEach`を使用します。これをビューファイルに追加しましょう。
```ejs
[label views/pages/index.ejs ]
...
<ul>
    <% mascots.forEach(function(mascot) { %>
        <li>
            <strong><%= mascot.name %></strong>
            representing <%= mascot.organization %>, born <%= mascot.birth_year %>
        </li>
    <% }); %>
</ul>
...
```

追加した新しい情報がブラウザで表示されています。

![node-ejs-templating-rendered](https://assets.digitalocean.com/articles/use-ejs-to-template-node-application/ejs-rendered.png)

### データをEJSのパーシャルに渡す

EJSパーシャルは、親ビューと全く同じデータにアクセスできます。ただし、パーシャルで変数を参照している場合、<^>パーシャルを使用するすべてのビューでそれを定義する必要がある<^>ので注意してください。さもないとエラーになります。

次のようなinclude構文でもEJSパーシャルに変数を定義したり渡したりできます。

```ejs
[label views/pages/about.ejs]
...
<header>
    <%- include('../partials/header', <^>{variant:'compact'}<^>); %>
</header>
...
```

しかしここでも、変数が定義されたと仮定することには注意が必要です。

必ずしも定義されていないかもしれないパーシャルの変数を参照し、初期値を付与したい場合、次のように入力します。

```ejs
[label views/partials/header.ejs]
...
<em>Variant: <%= typeof variant != 'undefined' ? variant : 'default' %></em>
...
```

上記では、EJSコードは、`変数`が定義されていればその値を、定義されていなければ`デフォルト値`をレンダリングします。

## まとめ

EJSは、そこまで複雑なものでなければ、アプリケーションのスピード開発に役立ちます。パーシャルを使用して変数を簡単にビューに渡せるので、大きなアプリケーションでも手軽に構築できます。

EJSの詳細については、[公式ドキュメント](https://ejs.co/#docs)を参照してください。