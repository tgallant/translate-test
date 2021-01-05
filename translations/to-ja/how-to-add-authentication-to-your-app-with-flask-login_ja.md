---
title: Flask-Login を使用してアプリケーションに認証を追加する方法
description: ユーザーがアプリケーションにログインできるようにすることは、Webアプリケーションに追加する最も一般的な機能の一つです。この記事では、Flask-Loginパッケージを使用してFlaskアプリケーションに認証を追加する方法を説明します。
original_id: 3576

---

### はじめに

ユーザーがアプリケーションにログインできるようにすることは、Webアプリケーションに追加する最も一般的な機能の一つです。この記事では、[Flask-Login](https://flask-login.readthedocs.io/en/latest/)パッケージを使用してFlaskアプリケーションに認証を追加する方法を説明します。

![Animated gif of the Flask app and login box](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/1.gif)

ログインしていないユーザーは見ることができない保護されたページに、ユーザーがログインしてアクセスできる、サインアップとログインページを構築します。ユーザーモデルから情報を取得し、ユーザーがログインした時にプロファイルがどのように見えるかを保護されたページに表示してシミュレーションします。

この記事では、次のことを説明します。

* セッション管理に Flask-Login ライブラリを使用
* 組み込み Flask ユティリティを使用して、パスワードをハッシュするときに使用
* ログインしているユーザーのみがアクセスできる保護されたページをアプリケーションに追加
* Flask-SQLAlchemy を使用して、ユーザーモデルを作成
* アカウントを作成してログインするユーザーのためにサインアップとログインフォームを作成
* 問題が発生したとき、ユーザーにエラーメッセージを戻す
* ユーザーのアカウントから情報を使用して、 プロファイルページに表示

[このプロジェクトのソースコードは、GitHub 入手可能です](https://github.com/PrettyPrinted/flask_auth_scotch)。

## 前提条件

このチュートリアルには、次が必要です。

* [ローカル環境にインストールされたPython](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3)。
* [基本的な Linux ナビゲーションとファイル管理の知識](https://www.digitalocean.com/community/tutorials/basic-linux-navigation-and-file-management)は役に立ちますが必要ではありません。
* [Visual Studio Code](https://www.digitalocean.com/community/tutorials/getting-started-with-python-in-visual-studio-code) などのエディターに精通することは役に立ちますが必要ではありません。

アプリケーションは、ブループリントにより Flask アプリケーションのファクトリーパターンを使用します。認証と関連するすべてを処理するブループリントがあり、インデックスと保護されたプロファイルページを含む通常のルートには別のものがあります。実際のアプリケーションでは、好きなように機能を分類することができますが、ここで説明されているソリューションはこのチュートリアルでうまく機能します。

チュートリアル完了後の、プロジェクトのファイル構造イメージは次のダイアグラムをご覧ください。

```
.
└── <^>flask_auth_app<^>
    └── project
        ├── __init__.py       # setup our app
        ├── auth.py           # the auth routes for our app
        ├── db.sqlite         # our database
        ├── main.py           # the non-auth routes for our app
        ├── models.py         # our user model
        └── templates
            ├── base.html     # contains common layout and links
            ├── index.html    # show the home page
            ├── login.html    # show the login form
            ├── profile.html  # show the profile page
            └── signup.html   # show the signup form
```

チュートリアルにおいて、こういったディレクトリとファイルを作成します。

## ステップ 1— パッケージをインストール

プロジェクトに必要な主要なパッケージが 3 つあります。

* Flask
* Flask-Login: 認証後のユーザーセッションを処理
* Flask-SQLAlchemy: データベースでユーザーモデルとインターフェースを表示

データベースの更なる依存関係をインストールする必要を避けるために、SQLite を使用します。

まず、プロジェクトディレクトリの作成から始めます。

```command
mkdir <^>flask_auth_app<^>
```

次に、プロジェクトディレクトリに移動する必要があります。

```command
cd <^>flask_auth_app<^>
```

プロジェクトディレクトリがない場合、Python 環境を作ります。マシンに Python がどのようにインストールされているかによって、次のようなコマンドになります。

```command
python3 -m venv <^>auth<^>
source <^>auth<^>/bin/activate
```

<$>[note]**注意:**`venv`[の設定については、ローカル環境に関連するチュートリアルを参照してください](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3)。 <$>

必要なパッケージをインストールするには、仮想環境から次のコマンドを実行します。

```custom_prefix((auth)$)
pip install flask flask-sqlalchemy flask-login
```

パッケージをインストールしたので、メインアプリケーションファイルを作成する準備ができました。

## ステップ 2 — メインアプリケーションファイルの作成

`プロジェクト`ディレクトリの作成から始めましょう。

```custom_prefix((auth)$)
mkdir project
```

最初にはじめるファイルは、プロジェクトの`__init__.py`ファイルになります。

```custom_prefix((auth)$)
nano project/__init__.py
```

このファイルには、データベースを初期化してブループリントを登録するというアプリケーションを作成する機能があります。現時点でそれほど必要ありませんが、アプリケーション完成には必要となります。ここでは SQLAlchemy を初期化し、設定値を設定し、ブループリントを登録する必要があります。

```python
[label project/__init__.py]
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

# init SQLAlchemy so we can use it later in our models
db = SQLAlchemy()

def create_app():
    app = Flask(__name__)

    app.config['SECRET_KEY'] = '<^>secret-key-goes-here<^>'
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite'

    db.init_app(app)

    # blueprint for auth routes in our app
    from .auth import auth as auth_blueprint
    app.register_blueprint(auth_blueprint)

    # blueprint for non-auth parts of app
    from .main import main as main_blueprint
    app.register_blueprint(main_blueprint)

    return app
```

メインアプリケーションファイルがあるので、ルート内で追加を開始します。

## ステップ 3 — ルートの追加

ルートについては、2 つのブループリントを使用します。メインブループリントについては、ログインの後にホームページ（`/`）とプロファイルページ （`/profile`）が作成されます。ユーザーがログインせずにプロファイルページにアクセスしようとする場合、ログインルートに戻ります。

認証ブループリントについては、ログインページ（`/login`）とサインアップページ（ `/sign-up`）の両方を取得するルートがあります。これらの 2 つのルートから POST 要求を処理するルートもあります。最後に、アクティブなユーザーをログアウトするログアウトルート（`/logout`）があります。

とりあえずは、簡単な返り値で`ログイン`、`サインアップ`、`ログアウト`を定義します。後にこれに再度アクセスし、希望の機能を使用して更新します。

まず、`main_blueprint` のために `main.py` を作成します。

```custom_prefix((auth)$)
nano project/main.py
```

```python
[label project/main.py]
from flask import Blueprint
from . import db

main = Blueprint('main', __name__)

@main.route('/')
def index():
    return 'Index'

@main.route('/profile')
def profile():
    return 'Profile'
```

次に、`auth_blueprint` のために `auth.py`を作成します。

```custom_prefix((auth)$)
nano project/auth.py
```

```python
[label project/auth.py]
from flask import Blueprint
from . import db

auth = Blueprint('auth', __name__)

@auth.route('/login')
def login():
    return 'Login'

@auth.route('/signup')
def signup():
    return 'Signup'

@auth.route('/logout')
def logout():
    return 'Logout'
```

端末では、`FLASK_APP` と `FLASK_DEBUG` の値を設定できます。

```custom_prefix((auth)$)
export FLASK_APP=project
export FLASK_DEBUG=1
```

`FLASK_APP` 環境変数は、アプリケーションをロードする方法を説明します。これは、`create_app` の場所を指します。ここでは、`プロジェクト`ディレクトリを指します。

`FLASK_DEBUG` 環境変数は、`1` に設定することで有効となります。 これにより、ブラウザでアプリケーションエラーを表示するデバッガを有効にします。

`<^>flask_auth_app<^>` ディレクトリに存在することを確認し、プロジェクトを実行します。

```custom_prefix((auth)$)
flask run
```

これでWebブラウザでは、5つの潜在的なURLに移動し、`auth.py  `と `main.py`  で定義されたテキストを参照できます。

たとえば、`localhost:5000/profile` にアクセスすると`Profile`を表示します。

![Screenshot of project at localhost port 5000 in browser](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/2.png)

ルートが予想通りに動作していることを確認したため、テンプレート作成に進みます。

## ステップ 4 —テンプレート作成

アプリケーションで使用されるテンプレートを作成しましょう。これは、実際のログイン機能を実装する前の最初のステップです。アプリケーションは 4つのテンプレートを使用します。

* index.html
* profile.html
* login.html
* signup.html

また、ページごとにコードがあるベーステンプレートもあります。この場合、ベーステンプレートには、ナビゲーションリンクとページの一般的なレイアウトがあります。それでは作成しましょう。

まず、`project`ディレクトリの下に`templates`ディレクトリを作成します。

```custom_prefix((auth)$)
mkdir -p project/templates
```

次に、`base.html` を作成します。

```custom_prefix((auth)$)
nano project/templates/base.html
```

次に、`base.html` ファイルに次のコードを追加します。

```html
[label project/templates/base.html]
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Flask Auth Example</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.2/css/bulma.min.css" />
</head>

<body>
    <section class="hero is-primary is-fullheight">

        <div class="hero-head">
            <nav class="navbar">
                <div class="container">

                    <div id="navbarMenuHeroA" class="navbar-menu">
                        <div class="navbar-end">
                            <a href="{{ url_for('main.index') }}" class="navbar-item">
                                Home
                            </a>
                            <a href="{{ url_for('main.profile') }}" class="navbar-item">
                                Profile
                            </a>
                            <a href="{{ url_for('auth.login') }}" class="navbar-item">
                                Login
                            </a>
                            <a href="{{ url_for('auth.signup') }}" class="navbar-item">
                                Sign Up
                            </a>
                            <a href="{{ url_for('auth.logout') }}" class="navbar-item">
                                Logout
                            </a>
                        </div>
                    </div>
                </div>
            </nav>
        </div>

        <div class="hero-body">
            <div class="container has-text-centered">
               {% block content %}
               {% endblock %}
            </div>
        </div>
    </section>
</body>

</html>
```

このコードは、アプリケーションとコンテンツが表示されるエリアの各ページに一連のメニューリンクを作成します。

<$>[note]**注意:**シーンの背後には、スタイリングとレイアウトを処理するために [Bulma](https://bulma.io/) を使用します。Bulma についての詳細は、公式の [Bulma  のドキュメント](https://bulma.io/documentation)を参照してください。<$>

次に、`templates/index.html` を作成します。

```custom_prefix((auth)$)
nano project/templates/index.html
```

ページにコンテンツを追加するために、新しいファイルを作成するには次のコードを追加します。

```html
[label project/templates/index.html]
{% extends "base.html" %}

{% block content %}
<h1 class="title">
  Flask Login Example
</h1>
<h2 class="subtitle">
  Easy authentication and authorization in Flask.
</h2>
{% endblock %}
```

このコードは、タイトルとサブタイトルを含む基本的なインデックスページを作成します。

次に、`templates/login.html` を作成します。

```custom_prefix((auth)$)
nano project/templates/login.html
```

このコードは、**メール**と**パスワード**のフィールドを含むログインページを生成します。セッション中のログインを「記憶」するためのチェックボックスもあります。

```html
[label project/templates/login.html]
{% extends "base.html" %}

{% block content %}
<div class="column is-4 is-offset-4">
    <h3 class="title">Login</h3>
    <div class="box">
        <form method="POST" action="/login">
            <div class="field">
                <div class="control">
                    <input class="input is-large" type="email" name="email" placeholder="Your Email" autofocus="">
                </div>
            </div>

            <div class="field">
                <div class="control">
                    <input class="input is-large" type="password" name="password" placeholder="Your Password">
                </div>
            </div>
            <div class="field">
                <label class="checkbox">
                    <input type="checkbox">
                    Remember me
                </label>
            </div>
            <button class="button is-block is-info is-large is-fullwidth">Login</button>
        </form>
    </div>
</div>
{% endblock %}
```

次に、`templates/signup.html` を作成します。

```custom_prefix((auth)$)
nano project/templates/signup.html
```

メール、名前、パスワードのフィールドを含むサインアップページを作成するには、次のコードを追加します。

```html
[label project/templates/signup.html]
{% extends "base.html" %}

{% block content %}
<div class="column is-4 is-offset-4">
    <h3 class="title">Sign Up</h3>
    <div class="box">
        <form method="POST" action="/signup">
            <div class="field">
                <div class="control">
                    <input class="input is-large" type="email" name="email" placeholder="Email" autofocus="">
                </div>
            </div>

            <div class="field">
                <div class="control">
                    <input class="input is-large" type="text" name="name" placeholder="Name" autofocus="">
                </div>
            </div>

            <div class="field">
                <div class="control">
                    <input class="input is-large" type="password" name="password" placeholder="Password">
                </div>
            </div>

            <button class="button is-block is-info is-large is-fullwidth">Sign Up</button>
        </form>
    </div>
</div>
{% endblock %}
```

次に、`templates/profile.html` を作成します。

```custom_prefix((auth)$)
nano project/templates/profile.html
```

このコードを追加して、**Anthony** を歓迎するためにハードコーディングされているタイトルを含むシンプルなページを作成します。

```html
[label project/templates/profile.html]
{% extends "base.html" %}

{% block content %}
<h1 class="title">
  Welcome, Anthony!
</h1>
{% endblock %}
```

その後、任意のユーザーを動的に迎えるためにコードを追加します。

テンプレートを追加したら、テキストではなくテンプレートを返す必要がある各ルート内のreturn文を更新できます。

次に、`index`と`profile`のインポートラインとルートを変更して `main.py` を更新します。

```python
[label project/main.py]
from flask import Blueprint<^>, render_template<^>
...
@main.route('/')
def index():
    return <^>render_template('index.html')<^>

@main.route('/profile')
def profile():
    return <^>render_template('profile.html')<^>
```

`login`と`signup`のインポートラインとルートを変更して、`auth.py` を更新します。

```python
[label project/auth.py]
from flask import Blueprint, render_template
...
@auth.route('/login')
def login():
    return <^>render_template('login.html')<^>

@auth.route('/signup')
def signup():
    return <^>render_template('signup.html')<^>
```

変更すると、`/sign-up` に移動する場合サインアップページは以下のように表示されます。

![Sign up page at /signup](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/3.png)

`/`、`/login`、`/profile` のページも表示できるはずです。

ここでは、完了したときにテンプレートが表示されないので、`/logout` は残します。

## ステップ 5 —ユーザーモデルの作成

ユーザーモデルは、アプリケーションにユーザーがいることを意味します。メールアドレス、パスワード、名前のフィールドがあります。アプリケーションでは、ユーザーごとに保存されるより多くの情報を決定することができます。誕生日、プロファイル画像、場所、またはユーザーの好みなどを追加できます。

Flask-SQLAlchemy で作成されたモデルはクラスにより表され、データベースのテーブルに変換されます。これらのクラスの属性はその後、こうしたテーブルの列に変わります。

次に進み、このユーザーモデルを作成しましょう。

```custom_prefix((auth)$)
nano project/models.py
```

このコードは、`id`、`email`、`password`、`name`の列があるユーザーモデルを作成します。

```python
[label project/models.py]
from . import db

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True) # primary keys are required by SQLAlchemy
    email = db.Column(db.String(100), unique=True)
    password = db.Column(db.String(100))
    name = db.Column(db.String(1000))
```

ユーザーモデルを作成した後は、データベースの設定に進みます。

## ステップ 6 — データベースの設定

前提条件に記載されているように、SQLite データベースを使用します。独自の SQLite データベースを作成できますが、Flask-SQLAlchemy を使用しましょう。`__init__.py` ファイルで指定されたデータベースのパスがあるため、Python REPLでデータベースを作成するには、Flask-SQLAlchemyを指示する必要があります。

アプリケーションを停止してPython REPLを開いた場合、`db` オブジェクトの`create_all` メソッドを使用してデータベースを作成できます。まだ仮想環境と`<^>flask_auth_app<^>`ディレクトリに存在していることを確認します。

```custom_prefix(>>>)
from project import db, create_app
db.create_all(app=create_app()) # pass the create_app result so Flask-SQLAlchemy gets the configuration.
```

<$>[note]**注意:** Python インタプリタを使用する場合には、[公式ドキュメント](https://docs.python.org/3/tutorial/interpreter.html)を参照してください。<$>

これで、プロジェクトディレクトリに`db.sqlite` が表示されます。このデータベースの中にはユーザーテーブルがあります。

## ステップ 7 — 認証機能の設定

サインアップ機能については、ユーザーのタイプをフォームに入力し、データベースに追加します。追加する前に、ユーザーがデータベースに存在しないことを確認する必要があります。ユーザーがいない場合は、プレーンテキストにパスワードを保存することは望ましくないため、データベースに追加する前にパスワードをハッシュすることを確認する必要があります。

POSTフォームデータを処理するために、2 つ目の関数を追加しましょう。この機能では、最初にユーザーから渡されたデータを収集します。

関数を作成して、下部にリダイレクトを追加します。これにより、より良いサインアップのユーザー体験と、ログインページに誘導されます。

インポートラインを変更し `signup_post` を実行することで `auth.py `を更新します。

```python
[label project/auth.py]
from flask import Blueprint, render_template<^>, redirect, url_for<^>
...
@auth.route('/signup', methods=['POST'])
def signup_post():
    # code to validate and add user to database goes here
    return redirect(url_for('auth.login'))
```

これで、ユーザーのサインアップに必要なコードの残りを追加しましょう。

まず、フォームデータを取得するために、リクエストオブジェクトを使用する必要があります。

インポートを追加して `signup_post` を実行するために `auth.py` の更新を続けます。

```python
[label auth.py]
from flask import Blueprint, render_template, redirect, url_for<^>, request<^>
from werkzeug.security import generate_password_hash, check_password_hash
from .models import User
from . import db
...
@auth.route('/signup', methods=['POST'])
def signup_post():
    email = request.form.get('email')
    name = request.form.get('name')
    password = request.form.get('password')

    user = User.query.filter_by(email=email).first() # if this returns a user, then the email already exists in database

    if user: # if a user is found, we want to redirect back to signup page so user can try again
        return redirect(url_for('auth.signup'))

    # create a new user with the form data. Hash the password so the plaintext version isn't saved.
    new_user = User(email=email, name=name, password=generate_password_hash(password, method='sha256'))

    # add the new user to the database
    db.session.add(new_user)
    db.session.commit()

    return redirect(url_for('auth.login'))
```

<$>[note]**注意:** plaintext へのパスワードの保存は、セキュリティの是正すべき実行と見なされます。一般的に、パスワードを安全に保つために複雑なハッシュアルゴリズムとパスワードソルトを使用します。<$>

## ステップ 8 — サインアップの方法のテスト

サインアップの方法が完了したため、新しいユーザーを作成することができます。フォームを使用して、ユーザーを作成します。

サインアップが機能したかどうかを確認する 2 つの方法があります。1つは、データベースビューアを使用して、テーブルに追加された行を調べます。または、同じメールアドレスで再度サインアップを試行します。エラーが発生した場合、最初のメールが適切に保存されたこととなります。では、そのアプローチをしてみましょう。

メールがすでに存在していることをユーザーに知らせ、ログインページに移動するようにユーザーに指示するためにコードを追加することができます。`flash`機能を呼び出すことで、次の要求にメッセージを送信します。この場合、リダイレクトとなります。このページに移動し、テンプレート内のメッセージにアクセスします。

まず、サインアップページにリダイレクトする前に`flash`を追加します。

```python
[label project/auth.py]
from flask import Blueprint, render_template, redirect, url_for, request<^>, flash<^>
...
@auth.route('/signup', methods=['POST'])
def signup_post():
    ...
    if user: # if a user is found, we want to redirect back to signup page so user can try again
        flash('Email address already exists')
        return redirect(url_for('auth.signup'))
```

テンプレートで点滅したメッセージを取得するには、フォーム上のこのコードを追加することができます。これは、フォーム上のメッセージを直接表示します。

```python
[label project/templates/signup.html]
...
{% with messages = get_flashed_messages() %}
{% if messages %}
    <div class="notification is-danger">
        {{ messages[0] }}. Go to <a href="{{ url_for('auth.login') }}">login page</a>.
    </div>
{% endif %}
{% endwith %}
<form method="POST" action="/signup">
```

![Sign up box showing a message that the "Email address already exists. Go to login page" in a dark pink box](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/4.png)

## ステップ 9 — ログイン方法の追加

ログイン方法は、ユーザー情報を取得しそれを使用して何かをするという点でサインアップ機能に似ています。この場合、入力されたメールアドレスを比較して、データベースに存在するかどうかを確認します。そのような場合、ユーザーが渡すパスワードをハッシュし、さらにデータベースのハッシュパスワードを比較して、提供されたパスワードをテストします。両方のハッシュパスワードが一致する場合、ユーザーが正しいパスワードを入力したことが分かります。

ユーザーがパスワードチェックに合格すると、ユーザーが正しい資格情報を持っていて、Flask-Loginを使用してログインすることができます。`login_user` を呼び出して、Flask-Loginはユーザーがログインしている間は継続するユーザーセッションを作成します。これにより、ユーザーは保護されたページを見ることができます。

POSTed データを扱う新しいルートから始めます。ユーザーが正常にログインすると、プロファイルページにリダイレクトします。

```python
[label project/auth.py]
...
@auth.route('/login', methods=['POST'])
def login_post():
    # login code goes here
    return redirect(url_for('main.profile'))
```

ユーザーが正しい資格を持っているか確認する必要があります。

```python
[label project/auth.py]
...
@auth.route('/login', methods=['POST'])
def login_post():
    email = request.form.get('email')
    password = request.form.get('password')
    remember = True if request.form.get('remember') else False

    user = User.query.filter_by(email=email).first()

    # check if the user actually exists
    # take the user-supplied password, hash it, and compare it to the hashed password in the database
    if not user or not check_password_hash(user.password, password):
        flash('Please check your login details and try again.')
        return redirect(url_for('auth.login')) # if the user doesn't exist or password is wrong, reload the page

    # if the above check passes, then we know the user has the right credentials
    return redirect(url_for('main.profile'))
```

テンプレート内のブロックに追加しましょう。ユーザーは点滅したメッセージを見ることができます。サインアップフォームと同様に、フォーム上の潜在的なエラーメッセージを追加しましょう。

```python
[label project/templates/login.html]
...
{% with messages = get_flashed_messages() %}
{% if messages %}
    <div class="notification is-danger">
        {{ messages[0] }}
    </div>
{% endif %}
{% endwith %}
<form method="POST" action="/login">
```

ユーザーが正常にログインしていることを伝える機能がありますが、ユーザーをログインするものはありません。ここで、ユーザーセッションを管理するために Flask-Login を取り入れます。

始める前に、Flask-Login の機能に必要なものがいくつかあります。ユーザーモデルに `UserMixin` を追加して開始します。`UserMixin`は、Flask-Loginの属性をモデルに追加します。そのため、Flask-Login は UserMixinと機能します。

```python
[label models.py]
from flask_login import UserMixin
from . import db

class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True) # primary keys are required by SQLAlchemy
    email = db.Column(db.String(100), unique=True)
    password = db.Column(db.String(100))
    name = db.Column(db.String(1000))
```

次に、ユーザーのローダーを指定する必要があります。*ユーザーローダー*は、セッションクッキーに保存されている ID から特定のユーザーを見つける方法を説明します。`create_app` 関数に、これと Flask-Login の `init` コードを追加できます。

```python
[label project/__init__.py]
...
from flask_login import LoginManager
...
def create_app():
    ...
    db.init_app(app)

    login_manager = LoginManager()
    login_manager.login_view = 'auth.login'
    login_manager.init_app(app)

    from .models import User

    @login_manager.user_loader
    def load_user(user_id):
        # since the user_id is just the primary key of our user table, use it in the query for the user
        return User.query.get(int(user_id))
```

最後に、セッションを作成するためにプロファイルページにリダイレクトする前に、`login_user` 機能を追加します。

```python
[label project/auth.py]
from flask_login import login_user
from .models import User
...
@auth.route('/login', methods=['POST'])
def login_post():
    ...
    # if the above check passes, then we know the user has the right credentials
    login_user(user, remember=remember)
    return redirect(url_for('main.profile'))
```

Flask-Login のセットアップにより、`/login` ルートを使用できます。すべてが配置されると、プロファイルページが表示されます。

![Profile page with "Welcome, Anthony!"](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/5.png)

## ステップ 10 — ページの保護

あなたの名前が **Anthony** でない場合、名前が間違っていることがわかります。望まれているのは、データベースに名前を表示するためのプロファイルです。まずページを保護し、ユーザーのデータにアクセスして名前を取得する必要があります。

Flask-Login を使用している時にページを保護するには、ルートと機能の間にある `@login_requried` デコレータを追加します。これにより、ログインしていないユーザーがルートを見ることを防ぎます。ユーザーがログインしていない場合、Flask-Login の設定に従ってログインページにリダイレクトされます。

`@login_required`デコレータでデコレートされたルートにより、関数の内部の `current_user` オブジェクトを使用する機能があります。この `current_user` はデータベースからのユーザーを表し、*ドット表記*によりそのユーザーのすべての属性にアクセスできます。たとえば、`current_user.email`、`current_user.password`、`current_user.name`、`current_user.id` は、ログイン中のユーザーのためにデータベースに保存された実際の値を戻します。

現在のユーザー名を使用してテンプレートに送信しましょう。次に、その名前を使用して値を表示します。

```python
[label project/main.py]
<^>from flask_login import login_required, current_user<^>
...
@main.route('/profile')
@login_required
def profile():
    return <^>render_template('profile.html', name=current_user.name)<^>
```

次に、`profile.html` ファイルに`名前`の値を表示するためにページを更新します。

```html
[label project/templates/profile.html]
...
<h1 class="title">
  Welcome, <^>{{ name }}<^>!
</h1>
```

プロファイルページに移動したら、ユーザー名が表示されることがわかります。

![User welcome page with the name of the currently logged-in user](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/6.png)

最後に、ログアウトビューを更新します。ログアウトのルートで、`logout_user` 機能を呼び出すことができます。最初からログインしていないユーザーをログアウトするのは意味がないので、`@login_required `デコレータがあります。

```python
[label project/auth.py]
<^>from flask_login import login_user, logout_user, login_required<^>
...
@auth.route('/logout')
@login_required
def logout():
    <^>logout_user()<^>
    return <^>redirect(url_for('main.index'))<^>
```

ログアウトしてプロファイルページを再び表示した後、エラーメッセージが表示されます。これは、ユーザーがページにアクセスできない場合に Flask-Login がメッセージを点滅させるためです。

![Login page with a message showing that user must log in to access page](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/7.png)

最後に、`if`文をテンプレートに入れて、ユーザーに関連するリンクのみを表示します。これによって、ログイン前にログインまたはサインアップするか選択することができます。ログインした後、プロファイルに移動またはログアウトできます。

```html
[label templates/base.html]
...
<div class="navbar-end">
    <a href="{{ url_for('main.index') }}" class="navbar-item">
        Home
    </a>
    {% if current_user.is_authenticated %}
    <a href="{{ url_for('main.profile') }}" class="navbar-item">
        Profile
    </a>
    {% endif %}
    {% if not current_user.is_authenticated %}
    <a href="{{ url_for('auth.login') }}" class="navbar-item">
        Login
    </a>
    <a href="{{ url_for('auth.signup') }}" class="navbar-item">
        Sign Up
    </a>
    {% endif %}
    {% if current_user.is_authenticated %}
    <a href="{{ url_for('auth.logout') }}" class="navbar-item">
        Logout
    </a>
    {% endif %}
</div>
```

![Home page with the Home, Login, and Sign Up nav at the top of the screen](https://assets.digitalocean.com/articles/how-to-add-authentication-to-your-app-with-flask-login/8.png)

これで、アプリケーションに認証を追加することができました。

## まとめ

Flask-Login と Flask-SQLAlchemy を使用してアプリケーションにログインシステムを構築しました。ユーザーモデルを作成し、さらにユーザー情報を保存することによって、ユーザーを認証する方法を説明しました。そして、フォームからパスワードをハッシュし、データベースに保存されているパスワードを比較することで、ユーザーのパスワードが正しいことを確認しました。最後に、プロファイルページの @`login_required `デコレータを使用してアプリケーションに認証を追加し、ログインしているユーザーだけがそのページを見ることができるようにしました。

このチュートリアルで作成したものは、小さなアプリケーションにおいては十分ですが、より多くの機能を最初から望む場合は、Flask-Login ライブラリの上部に構築されている Flask-User または Flask-Security ライブラリを使用することを検討してください。
