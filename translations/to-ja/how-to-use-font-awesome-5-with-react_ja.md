---
title: ReactでFont Awesome 5を使用する方法
description: Font Awesomeはウェブサイトでアイコンやソーシャルロゴを表示できるようにしてくれるツールキットです。Reactはユーザーインターフェースの作成に使用するJavaScriptのコードライブラリです。 Font AwesomeチームはReactのコンポーネントを組み込んでいますが、Font Awesome 5とその構造について、基本を理解しておくとよいでしょう。このチュートリアルでは、React Font Awesomeコンポーネントの使い方について学びます。
original_id: 3618

---

### はじめに

[Font Awesome](https://fontawesome.com/)はウェブサイトでアイコンやソーシャルロゴを提供してくれるウェブサイトのツールキットです。[React](https://reactjs.org/)はユーザーインターフェースの作成に使用するJavaScriptコードライブラリです。Font Awesomeチームは統合促進のため、[Reactコンポーネント](https://github.com/FortAwesome/react-fontawesome)を開発しましたが、Font Awesome 5とその構造について、基本を理解しておく必要があります。このチュートリアルでは、React Font Awesomeコンポーネントの使用方法を説明します。

![アイコンが表示されたFont Awesomeウェブサイト](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/1.png)

## 前提条件

このチュートリアルでコーディングは必要ありませんが、例をいくつか試すなら、次のものが必要です。

* ローカルにインストールしたNode.js。[Node.jsをインストールしてローカル開発環境を構築する方法](https://www.digitalocean.com/community/tutorial_series/how-to-install-node-js-and-create-a-local-development-environment)を参照してください。
* [Reactアプリの開発](https://github.com/facebook/create-react-app)。[Reactプロジェクトをセットアップする方法](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-react-project-with-create-react-app)を参照してください。

## ステップ1 — Font Awesomeを使用する

Font AwesomeチームはFont Awesomeと併用できるように、[Reactコンポーネント](https://github.com/FortAwesome/react-fontawesome)を開発しました。このライブラリについては、[アイコン選択](https://fontawesome.com/icons)後、チュートリアルに従ってください。

この例では、`home`アイコンを使用して、`App.js`ファイルですべてを行います。

```javascript
[label src/App.js]
import React from "react";
import { render } from "react-dom";

// get our fontawesome imports
import { faHome } from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/react-fontawesome";

// create our App
const App = () => (
  <div>
    <FontAwesomeIcon icon={faHome} />
  </div>
);

// render to #root
render(<App />, document.getElementById("root"));
```

アプリには今、小さな家のアイコンが表示されています。このコードでは`home`アイコンしか選択されず、バンドルサイズにアイコン1つ分が加わっただけであると分かります。

![homeアイコンが表示されたcode sandbox](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/2.png)

これでFont Awesomeは、このコンポーネントが一旦マウントされれば、SVGバージョンに置き換わることを確認します。

## ステップ2 — アイコンの選択

アイコンをインストールして使用する前に、Font Awesomeライブラリの構造を知っておくことが重要です。アイコンの数が多いため、複数のパッケージに分割することになりました。

どのアイコンを使うか選ぶ際、[Font Awesomeアイコンページ](https://fontawesome.com/icons)にアクセスして、選択肢を検討することをお勧めします。ページの左欄にさまざまなフィルタが表示されます。これらのフィルタは、アイコンのインポート元パッケージを示すので、大変重要です。

上記の例では、`@fortawesome/free-solid-svg-icons`パッケージから`home`アイコンを取り出しました。

### アイコンが属するパッケージを特定する

アイコンが属するパッケージは、左欄のフィルタを確認すればわかります。また、アイコンをクリックして、所属先パッケージを表示することもできます。

フォントの属するパッケージがわかったら、そのパッケージの3文字略語を覚えましょう。

* ソリッドスタイル - `fas`
* レギュラースタイル - `far`
* ライトスタイル - `fal`
* デュオトーンスタイル - `fad`

アイコンページから特定のタイプを検索できます。

![左欄にパッケージ名が表示されたアイコンページ。](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/3.png)

### 特定のパッケージのアイコンを使用する

[Font Awesomeアイコンページ](https://fontawesome.com/icons)を閲覧すると、大抵の場合、同じアイコンでも複数バージョンあるのが分かります。たとえば、`boxing-glove`アイコンを見てみましょう。

![3バージョンのboxing gloveアイコン](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/4.png)

特定のアイコンを使用するには、`<FontAwesomeIcon>`を調整します。さまざまなパッケージに属する複数のタイプの同じアイコンを以下に示します。各アイコンにはそれぞれ、先ほど触れた3文字略語が含まれます。

<$>[note]**注:**次の例は、数セクション先でアイコンライブラリを作成するまでは機能しません。<$>

ソリッドバージョンの一例：

```javascript
<FontAwesomeIcon icon={['fas', 'code']} />
```

タイプを指定しなければ、これがソリッドバージョンのデフォルトになります。

```javascript
<FontAwesomeIcon icon={faCode} />
```

`fal`を使用するライト版です。

```javascript
<FontAwesomeIcon icon={['fal', 'code']} />;
```

`icon `プロップを単純な文字列ではなく配列に変更ました。
<!-- Note: Need more context around why this was done and the significance to it. -->

## Font Awesomeのインストール

1つのアイコンに複数のバージョン、複数のパッケージ、無料/有料パッケージがあるので、フルインストールすると1`npm`パッケージより大きくなります。 パッケージを複数インストールしてから、必要なアイコンを選択するのがよいでしょう。

この記事では、すべてをインストールして、複数のパッケージをインストールする方法を説明します。

次のコマンドを実行してベースパッケージをインストールします。

```command
npm i -S @fortawesome/fontawesome-svg-core @fortawesome/react-fontawesome
```

次のコマンドを実行して、レギュラーアイコンをインストールします。

```command
# regular icons
npm i -S @fortawesome/free-regular-svg-icons
npm i -S @fortawesome/pro-regular-svg-icons
```

これらのコマンドでソリッドアイコンをインストールします。

```command
# solid icons
npm i -S @fortawesome/free-solid-svg-icons
npm i -S @fortawesome/pro-solid-svg-icons
```

このコマンドでライトアイコンをインストールします。

```command
# light icons
npm i -S @fortawesome/pro-light-svg-icons
```

このコマンドでデュオトーンアイコンをインストールします。

```command
# duotone icons
npm i -S @fortawesome/pro-duotone-svg-icons
```

最後に、このコマンドでブランドアイコンをインストールします。

```command
# brand icons
npm i -S @fortawesome/free-brands-svg-icons
```

あるいは、一度にすべてをインストールしたければ、このコマンドで無料のアイコンセットをインストールできます。

```command
npm i -S @fortawesome/fontawesome-svg-core @fortawesome/react-fontawesome @fortawesome/free-regular-svg-icons @fortawesome/free-solid-svg-icons @fortawesome/free-brands-svg-icons
```

Font Awesomeのプロアカウントがあれば、次のコマンドですべてのアイコンをインストールできます。

```command
npm i -S @fortawesome/fontawesome-svg-core @fortawesome/react-fontawesome @fortawesome/free-regular-svg-icons @fortawesome/pro-regular-svg-icons @fortawesome/free-solid-svg-icons @fortawesome/pro-solid-svg-icons @fortawesome/pro-light-svg-icons @fortawesome/pro-duotone-svg-icons @fortawesome/free-brands-svg-icons
```

パッケージはインストールしましたが、アプリケーションでの使用、アプリバンドルへの追加はこれからです。次のステップでその方法を見てみましょう。


## ステップ4 — アイコンライブラリの作成

複数のファイルにアイコンをインポートするのは面倒な作業です。たとえば、Twitterのロゴを数カ所で使用するとしましょう。何度も記述したくありません。

すべてを一箇所にインポートするには、各アイコンをファイルごとにインポートするのではなく、[Font Awesome library](https://github.com/FortAwesome/react-fontawesome#build-a-library-to-reference-icons-throughout-your-app-more-conveniently)を作成します。

`src`フォルダーに`fontawesome.js`を作成し、それを`index.js`にインポートしましょう。そのアイコンを使用したいコンポーネントがアクセス可能な範囲で（つまり子コンポーネント）、このファイルをどこでも好きな場所に置いてください。`index.js`や`App.js`でおこなってもうまくいきます。ただし、ファイルが巨大化するおそれがあるので、別ファイルに移動させた方がよいでしょう。

```javascript
[label src/fontawesome.js]
// import the library
import { library } from '@fortawesome/fontawesome-svg-core';

// import your icons
import { faMoneyBill } from '@fortawesome/pro-solid-svg-icons';
import { faCode, faHighlighter } from '@fortawesome/free-solid-svg-icons';

library.add(
  faMoneyBill,
  faCode,
  faHighlighter
  // more icons go here
);
```

独自のファイルで実施した場合、`index.js`にインポートする必要があります。

```javascript
[label src/index.js]
import React from 'react';
import { render } from 'react-dom';

// import your fontawesome library
import './fontawesome';

render(<App />, document.getElementById('root'));
```

### アイコンパッケージ全体のインポート

パッケージ全体をインポートするのはお勧めしません。アイコン一つ一つをアプリにインポートすると、バンドルサイズの巨大化を招くおそれがあるからです。パッケージ全体をインポートする必要があれば、もちろんそうすることもできます。

この例では、`@fortaesome/free-brands-svg-icon`にあるすべてのブランドアイコンをインポートするとしましょう。次のコマンドを入力してパッケージ全体をインポートします。

```javascript
[label src/fontawesome.js]
import { library } from '@fortawesome/fontawesome-svg-core';
import { fab } from '@fortawesome/free-brands-svg-icons';

library.add(fab);
```

`fab`はブランドアイコン全体を表します。

### アイコンの個別インポート

Font Awesomeアイコンは、バンドルサイズを極力軽くするために、ひとつずつ、必要なアイコンのみインポートすることをお勧めします。

次のように、さまざまなパッケージのアイコンで構成されるライブラリを作成できます。

```javascript
[label src/fontawesome.js]
import { library } from '@fortawesome/fontawesome-svg-core';

import { faUserGraduate } from '@fortawesome/pro-light-svg-icons';
import { faImages } from '@fortawesome/pro-solid-svg-icons';
import {
  faGithubAlt,
  faGoogle,
  faFacebook,
  faTwitter
} from '@fortawesome/free-brands-svg-icons';

library.add(
  faUserGraduate,
  faImages,
  faGithubAlt,
  faGoogle,
  faFacebook,
  faTwitter
);
```

### 複数のスタイルから同じアイコンをインポートする

`fal`、`far`、`fas`パッケージにある全タイプの`boxing-glove`をインポートしたければ、別の名前を付けることで、すべてインポートして追加できます。

```javascript
[label src/fontawesome.js]
import { library } from '@fortawesome/fontawesome-svg-core';
import { faBoxingGlove } from '@fortawesome/pro-light-svg-icons';
import {
  faBoxingGlove as faBoxingGloveRegular
} from '@fortawesome/pro-regular-svg-icons';
import {
  faBoxingGlove as faBoxingGloveSolid
} from '@fortawesome/pro-solid-svg-icons';

library.add(
    faBoxingGlove,
    faBoxingGloveRegular,
    faBoxingGloveSolid
);
```

これで、異なるプレフィックスを付けてこれらのアイコンを使用できます。

```javascript
<FontAwesomeIcon icon={['fal', 'boxing-glove']} />
<FontAwesomeIcon icon={['far', 'boxing-glove']} />
<FontAwesomeIcon icon={['fas', 'boxing-glove']} />
```

## ステップ5 — アイコンの使用

必要なものをインストールし、アイコンをFont Awesomeライブラリに追加したので、これらを使用してサイズを割り当てる準備が整いました。このチュートリアルでは、ライト（`fal`）パッケージを使用します。

1つ目の例では、通常のサイズを使用します。

```javascript
<FontAwesomeIcon icon={['fal', 'code']} />
```

2つ目の例では、_サイズ名_を使用します。サイズ名には、小（`sm`）、中（`md`）、大（`lg`）、特大（`xl`）を略称で使用します。

```javascript
<FontAwesomeIcon icon={['fal', 'code']} size="sm" />
<FontAwesomeIcon icon={['fal', 'code']} size="md" />
<FontAwesomeIcon icon={['fal', 'code']} size="lg" />
<FontAwesomeIcon icon={['fal', 'code']} size="xl" />
```

3つ目の例では、サイズ番号（最大`6`）を使用します。

```javascript
<FontAwesomeIcon icon={['fal', 'code']} size="2x" />
<FontAwesomeIcon icon={['fal', 'code']} size="3x" />
<FontAwesomeIcon icon={['fal', 'code']} size="4x" />
<FontAwesomeIcon icon={['fal', 'code']} size="5x" />
<FontAwesomeIcon icon={['fal', 'code']} size="6x" />
```

サイズ番号を使用する際、小数点を使用して完璧なサイズを得ることもできます。

```javascript
<FontAwesomeIcon icon={['fal', 'code']} size="2.5x" />
```

Font AwesomeはCSSの文字色を利用して、使用するSVGをスタイリングします。このアイコンを置く場所に `<p>`タグを配置すると、その段落の色がアイコンの色になります。

```javascript
<FontAwesomeIcon icon={faHome} style={{ color: 'red' }} />
```

Font Awesomeにある[power transforms](https://github.com/FortAwesome/react-fontawesome#advanced)機能を使用すると、さまざまな変形をつなぎ合わせられます。

```javascript
<FontAwesomeIcon icon={['fal', 'home']} transform="down-4 grow-2.5" />
```

[Font Awesomeサイトにある変形なら何で](https://fontawesome.com/how-to-use/on-the-web/styling/power-transforms)も使用できます。これを使用すると、アイコンを上下左右に動かしたり、文字の横でもボタンの内部でも最適な位置に配置できます。

### アイコンの幅を固定する

一箇所で複数のアイコンを、同じ幅で揃えて使用するには、Font Awesomeの`fixedWidth `のプロップが使用できます。。たとえば、ナビゲーションドロップダウン用に幅を固定させる必要があるとしましょう。

![ドロップダウンメニューとハイライトされた「Courses」が表示されたスコッチウェブサイト](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/5.png)

```javascript
<FontAwesomeIcon icon={['fal', 'home']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'file-alt']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'money-bill']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'cog']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'usd-square']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'play-circle']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'chess-king']} fixedWidth />
<FontAwesomeIcon icon={['fal', 'sign-out-alt']} fixedWidth />
```

### アイコンを回転させる

フォームが処理中の際、フォームボタンに回転機能を実装すると便利です。ロード中を効果的に示す回転アイコンを使用できます。

```javascript
<FontAwesomeIcon icon={['fal', 'spinner']} spin />
```

`spin` プロップは何にでも使用できます。

```javascript
<FontAwesomeIcon icon={['fal', 'code']} spin />
```

### 拡張機能：アイコンをマスクする

Font Awesomeでは2つのアイコンを組み合わせて[マスキング](https://fontawesome.com/how-to-use/on-the-web/styling/masking)効果を施せます。1つ目の通常アイコンを定義し、次に`mask`プロップを使用して2つ目のアイコンを定義して上部に配置します。1つ目のアイコンが、マスキングアイコンの下で抑制されます。

この例では、マスキングを使用してタグフィルターを作成しました。

![Font Awesomeのタグフィルター](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/6.png)

```javascript
<FontAwesomeIcon
  icon={['fab', 'javascript']}
  mask={['fas', 'circle']}
  transform="grow-7 left-1.5 up-2.2"
  fixedWidth
/>
```

複数の`transform` プロップをどのようにつなげ、下部のアイコンを移動させてマスキングアイコンに合わせているかに注目してください。

Font Awesomeで背景ロゴまで色付け、変更しています。

![再びタグフィルター、今度は青色の背景で](https://assets.digitalocean.com/articles/how-to-use-font-awesome-5-with-react/7.png)

## ステップ6 — Reactの外で`react-fontscape`とアイコンを使用する

サイト全体が単ページのアプリケーション（SPA）ではなく、従来型のサイトが設けられ、[Reactがトップに追加](https://scotch.io/tutorials/how-to-sprinkle-reactjs-into-an-existing-web-application)されているとします。Font Awesomeは、メインのSVG/JSライブラリと`react-fontawesome`ライブラリのインポートを避け、Reactライブラリを使用してReactコンポーネントの外部のアイコンを監視する方法を編み出しました。

`<i class="fas fa-stroopwafel"></i>`がある場合、次のようにそれらのアイコンを監視・更新するよう、Font Awesomeに指示します。

```javascript
import { dom } from '@fortawesome/fontawesome-svg-core'

dom.watch() // This will kick off the initial replacement of i to svg tags and configure a MutationObserver
```

[MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)は、DOMの変更の常時監視を実現するWebテクノロジーです。この技術については、[Reactフォント Awesomeドキュメント](https://fontawesome.com/how-to-use/on-the-web/using-with/react)を参照してください。

## まとめ

Font AwesomeとReactの併用は素晴らしい組み合わせですが、複数のパッケージを使用し、異なる組み合わせを検討する必要が生じます。このチュートリアルでは、Font AwesomeとReactを併用する方法をいくつか説明しました。