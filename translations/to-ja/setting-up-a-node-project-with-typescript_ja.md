---
title: Typescriptでノードプロジェクトをセットアップする方法
description: サーバーサイドJavaScriptの記述は、コードベースが肥大化していくので困難な作業です。TypeScriptは、大規模なJavaScriptプロジェクトの構築・管理に役立つ型付け（オプション）スーパーセットです。これは強力な静的型付け、コンパイル、オブジェクト指向プログラミングなどの追加機能を持つJavaScriptとも考えられます。このチュートリアルでは、[Express](https://expressjs.com/)フレームワークのTypeScriptとの併用方法を説明します。
original_id: 3569

---

### はじめに

[Node](https://nodejs.org/en/)は、サーバーサイド[JavaScript](https://www.javascript.com/)の記述を可能にする[ランタイム](https://en.wikipedia.org/wiki/Runtime_system)環境です。2011年のリリース以来、広く採用されています。サーバーサイドJavaScriptの記述は、JavaScript言語の性質上、[動的](https://en.wikipedia.org/wiki/Programming_language#Static_versus_dynamic_typing)で[弱い](https://en.wikipedia.org/wiki/Programming_language#Weak_and_strong_typing)型付けなので、コードベースが肥大化していき、困難な作業です。

他の言語をからJavaScriptを始める開発者は時に、静的で強い型付けに欠けている面に不満を感じますが、TypeScriptはそのギャップを埋めてくれます。

[TypeScript](https://www.typescriptlang.org/)は、大規模なJavaScriptプロジェクトの構築・管理に役立つ型付け（オプション）スーパーセットです。静的で強い型付け、コンパイル、オブジェクト指向プログラミングなどの追加機能を持つJavaScriptとして考えられます。

<$>[note]**注:** TypeScriptは、技術的にはJavaScriptのスーパーセットです。つまりJavaScriptコードはすべて、有効なTypeScriptコードであるといえます。<$>

以下にTypeScriptを使用するメリットを挙げます。

1. オプションの静的型付け。
2. 型推論。
3. インターフェースが利用可能。

このチュートリアルでは、NodeプロジェクトとTypeScriptをセットアップします。TypeScriptを使用して[Express](https://expressjs.com)アプリケーションを構築し、それを整然とした信頼性の高いJavaScriptコードにトランスパイルします。

## 前提条件

このガイドを始める前に、Node.jsをマシンにインストールします。ガイド[Node.jsをインストールしてローカル開発環境を整える](https://www.digitalocean.com/community/tutorial_series/how-to-install-node-js-and-create-a-local-development-environment)を参照して、これを実施してください。

## ステップ1 — npmプロジェクトの初期化

まず、`<^>node_project<^>`という新しいフォルダを作成し、そのディレクトリに移動します。

```command
mkdir <^>node_project<^>
cd <^>node_project<^>
```

次に、npmプロジェクトとしてそのフォルダを初期化します。

```command
npm init
```

`npm init`を実行した後、プロジェクトに関する情報をnpmに提供します。npmに妥当なデフォルトを推論させたければ、`y`フラグを付けて追加情報のプロンプトをスキップできます。

```command
npm init -y
```

プロジェクトスペースがセットアップされ、必要な依存関係をインストールする準備が整いました。

## ステップ2 — 依存関係をインストールする

最小限のnpmプロジェクトが初期化されると、次のステップは、[TypeScript](https://www.typescriptlang.org/)の実行に必要な依存関係のインストールです。

プロジェクトディレクトリから次のコマンドを実行して、依存関係をインストールします。

```command
npm install -D typescript@3.3.3
npm install -D tslint@5.12.1
```

`-D`フラグは、 `--save-dev`のショートカットです。このフラグの詳細については[npmjsドキュメント](https://docs.npmjs.com/cli/install)を参照してください。

それでは[Express](https://expressjs.com/)フレームワークをインストールしましょう。

```command
npm install -S express@4.16.4
npm install -D @types/express@4.16.1
```

2つ目のコマンドは、TypeScriptをサポートするExpress*のタイプ*をインストールします。TypeScriptのタイプはファイルで、通常は`.d.ts`という拡張子が付きます。これらのファイルはAPIに関するタイプ情報（この場合はExpressフレームワーク）の提供に使用します。

TypeScriptと Expressは独立したパッケージなので、このパッケージが必要です。`@type/express`パッケージがなければ、TypeScriptがExpressクラスのタイプを知る方法はありません。


## ステップ3 — TypeScriptの設定

このセクションでは、TypeScriptをセットアップしてTypeScriptのLintチェックを設定します。TypeScriptは、`tsconfig.json`というファイルを使用して、プロジェクトのコンパイラオプションを設定します。プロジェクトのrootディレクトリに`tsconfig.json`ファイルを作成し、次のスニペットを貼り付けます。

```json
[label tsconfig.json]
{
  "compilerOptions": {
    "module": "commonjs",
    "esModuleInterop": true,
    "target": "es6",
    "moduleResolution": "node",
    "sourceMap": true,
    "outDir": "dist"
  },
  "lib": ["es2015"]
}
```

上記JSONスニペットの重要部分を見ていきしょう。

* `module`: モジュールコード生成方法を指定します。Nodeは`commonjs`を使用します。
* `target`:出力言語レベルを指定します。
* `moduleResolution`: インポートが参照するものをコンパイラに理解させます。値`node`は、Node module resolution機構を模倣します。 　
* `outDir`: トランスパイル後の`.js`ファイルの出力先です。このチュートリアルでは、`dist`として保存します。

`tsconfig.json`ファイルを手動で作成、記入する代わりに次のコマンドを実行することもできます。

```command
tsc --init
```

このコマンドは、コメント付きの`tsconfig.json`ファイルを生成します。

利用可能なキー値オプションの詳細について詳しく知るには、公式[TypeScriptドキュメント](https://www.typescriptlang.org/docs/handbook/compiler-options.html)を参照してください。

これで、プロジェクトのTypeScript Lintチェックを設定できます。プロジェクトのrootディレクトリで稼働しているターミナル、つまりこのチュートリアルが`<^>node_project<^>`として確立したターミナルで、次のコマンドを実行して`tslint.json`ファイルを生成します。

```command
./node_modules/.bin/tslint --init
```

新たに生成された`tslint.json`ファイルを開いて、`no-console`ルールを追加します。

```json
[label tslint.json]
{
  "defaultSeverity": "error",
  "extends": ["tslint:recommended"],
  "jsRules": {},
  "rules": {
    "no-console": false
  },
  "rulesDirectory": []
}
```

TypeScript Lintツールはデフォルト`でコンソール`ステートメントを使用したデバッグを妨げるため、Lintツールにデフォルトの`no-console`ルールを無効にするよう明示的に指示する必要があります。

## ステップ4 — `package.json`ファイルの更新

この時点で、ターミナルで関数を個別に実行するか、[npmスクリプト](https://docs.npmjs.com/misc/scripts)を作成して実行することができます。

このステップでは、TypeScriptコードをコンパイルしてトランスパイルする`start`スクリプトを作成し、結果の`.js`アプリケーションを実行します。

`package.json`ファイルを開き、適宜更新します。

```json
[label package.json]
{
  "name": "node-with-ts",
  "version": "1.0.0",
  "description": "",
  "main": "dist/app.js",
  "scripts": {
    "start": "tsc && node dist/app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/express": "^4.16.1",
    "tslint": "^5.12.1",
    "typescript": "^3.3.3"
  },
  "dependencies": {
    "express": "^4.16.4"
  }
}
```

上記のスニペットでは、`main`パスを更新し、`start`コマンドを[scripts](https://docs.npmjs.com/misc/scripts)セクションに追加しています。`start`コマンドを見ると、最初に`tsc`コマンドが、続いて`node`コマンドが実行されるのがわかります。このコマンドは、`node`で生成された出力をコンパイルし、実行します。

`tsc`コマンドは、`tsconfig.json`ファイルの設定通り、アプリケーションをコンパイルし、生成された`.js`出力を指定した`outDir`ディレクトリに配置するよう、TypeScriptに指示します。

## ステップ5 — Basic Expressサーバーの作成と実行

TypeScriptとそのlintツールが設定されたので、次はNode Expressサーバーを構築しましょう。

まず、プロジェクトのrootディレクトリに`src`フォルダを作成します。

```command
mkdir src
```

次に、そのフォルダ内に`app.ts`という名前のファイルを作成します。

```command
touch src/app.ts
```

この時点で、フォルダ構造は次のように見えるはずです。

```
├── node_modules/
├── src/
  ├── app.ts
├── package-lock.json
├── package.json
├── tsconfig.json
├── tslint.json
```

`app.ts`ファイルを任意のテキストエディタで開き、次のコードスニペットを貼り付けます。

```typescript
[label src/app.ts]
import express from 'express';

const app = express();
const port = 3000;
app.get('/', (req, res) => {
  res.send('The sedulous hyena ate the antelope!');
});
app.listen(port, err => {
  if (err) {
    return console.error(err);
  }
  return console.log(`server is listening on ${port}`);
});
```

上記のコードは、ポート`3000`でリクエストをlistenするノードサーバーを作成します。次のコマンドを使用してアプリケーションを実行します。

```command
npm start
```

実行が成功すると、メッセージがターミナルに表示されます。

```command
[secondary_label Output]
server is listening on 3000
```

これで、ブラウザで`http://localhost:3000`にアクセスできます。次のメッセージが表示されます。

```command
[secondary_label Output]
The sedulous hyena ate the antelope!
```

![メッセージ「The sedulous hyena ate the antelope!」を表示するブラウザウィンドウ](https://assets.digitalocean.com/articles/setting-up-a-node-project-with-typescript/1.png)

`dist/app.js`ファイルを開くと、TypeScriptコードのトランスパイル版が表示されます。

```javascript
[label dist/app.js]
"use strict";

var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
};
Object.defineProperty(exports, "__esModule", { value: true });
const express_1 = __importDefault(require("express"));
const app = express_1.default();
const port = 3000;
app.get('/', (req, res) => {
    res.send('The sedulous hyena ate the antelope!');
});
app.listen(port, err => {
    if (err) {
        return console.error(err);
    }
    return console.log(`server is listening on ${port}`);
});

//# sourceMappingURL=app.js.map
```

この時点で、ノードプロジェクトがTypeScriptを使用するように、正常にセットアップされました。

## まとめ

このチュートリアルでは、TypeScriptが信頼性の高いJavaScriptのコードを作成するのに役立つ理由について学びました。さらに、TypeScriptで作業するメリットについても学びました。

最後に、NodeプロジェクトのセットアップにはExpressフレームワークを使用しましたが、プロジェクトのコンパイル・実行にはTypeScriptを使用しました。