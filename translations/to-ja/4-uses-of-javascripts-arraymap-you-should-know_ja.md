---
title: JavaScript の配列項目から反復するための .map() の使用方法
description: "JavaScript のデータセットから反復する最も一般的な方法の1つは、`.map()` メソッドです。 `.map() `は、親配列内の各項目に特定の関数の呼び出しから配列を作成します。 `.map()` は元の変更ではなく、新しい配列を作成する非変異メソッドです。このチュートリアルでは、JavaScript の  `.map()` の 4 つの注目に値する使用を見てみましょう。配列要素の呼び出し、文字列を配列に変換、ライブラリのレンダリングリスト、配列オブジェクトの再フォーマットを行います。"
original_id: 3609

---

### はじめに

古典的な `forloop` から` forEach() `メソッドまで、JavaScript のデータセットから反復するためにさまざまなテクニックとメソッドを使用します。最も一般的なメソッドの1つは、`.map()` メソッドです。`.map()` は、親配列内の各項目に特定の関数を呼び出すことから配列を作成します。配列の呼び出しだけを変化させる非変異メソッドに反するものとして、`.map( )` は新しい配列を作成する非変異メソッドです。

このメソッドには、配列で作業するときに多くの用途がある場合があります。このチュートリアルでは、JavaScript の `.map()` の 4 つの注目に値する使用を見てみましょう。配列要素の呼び出し、文字列を配列に変換、ライブラリのレンダリングリスト、配列オブジェクトの再フォーマットを行います。

## 前提条件

このチュートリアルではコーディングは必要ありませんが、例とともに次のものに興味がある場合、[Node.js REPL](https://nodejs.dev/how-to-use-the-nodejs-repl) またはブラウザ開発者ツールを使用することもできます。

* Node.js をローカルにインストールするには、[Node.js のインストール、ローカル開発環境を作成する方法](https://www.digitalocean.com/community/tutorial_series/how-to-install-node-js-and-create-a-local-development-environment)を参照してください。

* [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/console/javascript) は、[Google Chrome](https://www.google.com/chrome/) の最新バージョンのダウンロードとインストールによって使用できます。


## ステップ 1 — 配列内の各項目の関数を呼び出す

`.map()` はコールバック関数を引数の 1 つとして受け入れ、その関数の重要なパラメータは関数によって処理される項目の現在の値です。これは必要なパラメータです。このパラメータにより、配列内の各項目を変更し、新しい関数を作成できます。

次に例を示します。

```js
const sweetArray = [2, 3, 4, 5, 35]
const sweeterArray = sweetArray.map(sweetItem => {
    return sweetItem * 2
})

console.log(sweeterArray)
```

この出力はコンソールにログインします。

```
[secondary_label Output]
[ 4, 6, 8, 10, 70 ]
```

これにより、さらに簡素化できます。

```js
// create a function to use
const makeSweeter = sweetItem => sweetItem * 2;

// we have an array
const sweetArray = [2, 3, 4, 5, 35];

// call the function we made. more readable
const sweeterArray = sweetArray.map(makeSweeter);

console.log(sweeterArray);
```

同様の出力はコンソールにログインします。

```
[secondary_label Output]
[ 4, 6, 8, 10, 70 ]
```

`sweetArray.map(makeSweeter)` のようなコードがあなたのコードを少し読みやすくします。

## ステップ 2 — 文字列を配列に変換

`.map()` は配列プロトタイプに属することが知られています。このステップでは、文字列を使用して配列に変換します。ここでは文字列の機能のメソッドを開発しません。むしろ、特別な`.call()` メソッドを使用します。

JavaScript のすべてはオブジェクトであり、メソッドはこうしたオブジェクトに付随する関数です。`.call()` により、別のオブジェクトのコンテキストを使用できます。したがって、配列の `.map()` のコンテキストを文字列にコピーします。

`.call()` は、使用するコンテキストの引数と元の関数の引用のパラメータを渡すことができます。

次に例を示します。

```js
const name = "Sammy"
const map = Array.prototype.map

const newName = map.call(name, eachLetter => {
    return `${eachLetter}a`
})

console.log(newName)
```

この出力はコンソールにログインします。

```command
[secondary_label Output]
[ "Sa", "aa", "ma", "ma", "ya" ]
```

ここでは、文字列の `.map()` のコンテキストを使用して、`.map()` が予期する関数の引数を渡しています。

この関数は、文字列の `.split()` メソッドのようなもので、配列に戻す前に各文字列の文字が変更できるだけです。

## ステップ 3 — JavaScript ライブラリのレンダリングリスト

[React](https://reactjs.org/) のような JavaScript ライブラリは、リストの項目をレンダリングするために  `.map()` を使用します。しかし、`.map()` メソッドは JSX 構文に含まれるため、これは JSX 構文を必要とします。

React コンポーネントの例は次のとおりです。

```js
import React from "react";
import ReactDOM from "react-dom";

const names = ["whale", "squid", "turtle", "coral", "starfish"];

const NamesList = () => (
  <div>
    <ul>{names.map(name => <li key={name}> {name} </li>)}</ul>
  </div>
);

const rootElement = document.getElementById("root");
ReactDOM.render(<NamesList />, rootElement);
```

これは、React のステートレスなコンポーネントであり、リストにより `div` をレンダリングします。各リスト項目は、最初に作成された名前の配列を繰り返すため、`.map()` を使用してレンダリングされます。このコンポーネントは、`root` の `ID` を使用して DOM 要素の ReactDOM を使用します。

## ステップ 4 — 配列オブジェクトの再フォーマット

`.map()` は、配列のオブジェクトを反復するために使用し、従来の配列に似た方法で、各オブジェクトの内容を変更し新しい配列を戻します。この変更は、コールバック関数で戻されるものに基づいて行われます。

次に例を示します。

```js
const myUsers = [
    { name: 'shark', likes: 'ocean' },
    { name: 'turtle', likes: 'pond' },
    { name: 'otter', likes: 'fish biscuits' }
]

const usersByLikes = myUsers.map(item => {
    const container = {};

    container[item.name] = item.likes;
    container.age = item.name.length * 10;

    return container;
})

console.log(usersByLikes);
```

この出力はコンソールにログインします。

```
[secondary_label Output]
[
    {shark: "ocean", age: 50},
    {turtle: "pond", age: 60},
    {otter: "fish biscuits", age: 50}
]
```

ここでは、ブラケットとドット表記を使用して、配列の各オブジェクトを修正します。このユースケースは、フロントエンドアプリケーションで保存または解析する前に、受信したデータを処理または縮約するために使用できます。

## まとめ

このチュートリアルでは、JavaScript の  `.map()` メソッドの 4 つの使用を見てきました。他のメソッドと組み合わせて、`.map()` の機能は拡張できます。詳しくは、[JavaScript: 反復メソッドの記事にある配列メソッドの使用方法](https://www.digitalocean.com/community/tutorials/how-to-use-array-methods-in-javascript-iteration-methods)を参照してください。