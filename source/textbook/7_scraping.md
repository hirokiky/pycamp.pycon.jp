```{index} Scraping
```

# Web API、スクレイピング

```{admonition} 節サブタイトル
自動でデータを収集する方法
```

## Web APIとスクレイピングとは

**Web API** はインターネット上に用意されているAPIをプログラムから呼び出す技術のことです。
**スクレイピング** はウェブサイトから情報を抽出する、コンピュータソフトウェア技術のことをいいます。

ここではPythonを使った実用的なプログラムの例として、Web APIとスクレイピングの演習を行います。

## 環境構築

前章の「 {ref}`about-venv` 」を参考に、venvモジュールを利用して、スクレイピング用のvenv環境を構築します。
venv環境を `activate` コマンドで有効にし、Web APIとスクレイピングに使用する **Requests** と **Beautiful Soup 4** を `pip` コマンドでインストールします。

```{code-block} sh
:caption: "演習用のvenv環境を構築(macOS、Linux)"

$ mkdir scraping
$ cd scraping
$ python3 -m venv env
$ source env/bin/activate
(env) $ pip install requests
(env) $ pip install beautifulsoup4
```

```{code-block} sh
:caption: "演習用のvenv環境を構築(Windows)"

> mkdir scraping
> cd scraping
> python -m venv env
> env\Scripts\Activate.ps1
(env) > pip install requests
(env) > pip install beautifulsoup4
```

```{index} Requests
```

### Requests


* URL: <https://requests.readthedocs.io/>

Requests について簡単に紹介します。
Requests はウェブサイトにアクセスしてHTMLなどのデータを取得するためのライブラリです。
Pythonの標準ライブラリ [urllib.request](https://docs.python.org/ja/3/library/urllib.request.html) でも同様のことは行なえますが、より便利な Requests をここでは使用します。

```{index} Beautiful Soup 4
```

### Beautiful Soup 4

* URL: <https://www.crummy.com/software/BeautifulSoup/bs4/doc/>

Beautiful Soup 4はHTMLやXMLの中身を解析して、任意の情報を取得するためのライブラリです。
Pythonの標準ライブラリ [html.parser](https://docs.python.org/ja/3/library/html.parser.html) でも同様のことは行なえますが、より便利な Beautiful Soup 4 をここでは使用します。
なお、beautifulsoupとbeautifulsoup4が存在しますが、新しい **beautifulsoup4** を使うようにしてください。

## シンプルなWeb APIのコード

Web APIの例としてconnpassのAPIを実行して、pythonというキーワードを含んだ2023年5月に開催されるイベント情報を取得します。

- [APIリファレンス - connpass](https://connpass.com/about/api/)

下記のコードを `events.py` という名前で保存します({numref}`events-py`)。

(events-py)=

```{literalinclude} events.py
:caption: events.py
```

このコードを実行すると、以下のようにイベントタイトルと日付の一覧が取得できます({numref}`exec-events-py`)。

(exec-events-py)=

```{code-block} bash
:caption: "connpass APIを実行"

(env) $ python events.py
件数: 10
【音声のみ】第188回プログラミング初学者歓迎もくもく会＋交流会
2023-05-08T19:00:00+09:00
【第15回・WeWork原宿】もくもくスキルアップしよっ会
2023-05-27T13:00:00+09:00
3D何でも勉強会 #2
2023-05-27T14:00:00+09:00
:
```

### コードの解説

上記のコードがどういった内容なのかを解説します。

- Web APIを実行するために `requests` をインポートします

```{literalinclude} events.py
:caption: モジュールのインポート
:lines: 1
```

- メインとなる処理を `main` 関数として定義しています。 なお、関数の名前に特に決まりはなく、必ずしも `main` である必要はありません。

```{literalinclude} events.py
:caption: main()関数の定義
:lines: 4
```

- APIのパラメーターとしてキーワードに `python` を、範囲に `202305` を指定します。パラメーターを書き換えれば検索条件が変わります。

```{literalinclude} events.py
:caption: パラメーターを作成
:lines: 5-8
```

- `requests.get()` にURLとパラメーターを指定して結果を取得します。
- 結果はJSON形式で返ってくるので、 `.json()` メソッドでPythonのデータ型（辞書、リスト等）に変換します。

```{literalinclude} events.py
:caption: Web APIを実行して結果を取得
:lines: 9-12
```

- Pythonデータ型のイベント情報から、件数とイベント名、開催日を取得して出力します。

```{literalinclude} events.py
:caption: 件数とイベント名、開催日を出力
:lines: 14-17
```

- 最後に、このスクリプトが実行された時に、main()関数を実行するように指定します。

```{literalinclude} events.py
:caption: main()関数を実行
:lines: 20-21
```

## シンプルなスクレイピングのコード

スクレイピングの例として、docs.python.orgの組み込み関数一覧のページ(<https://docs.python.org/ja/3.10/library/functions.html>)から関数名の情報を抜き出します。

```{figure} images/func-list.png
:width: 50%

組み込み関数一覧ページ
```

下記コードを `funcs.py` という名前で保存します({numref}`funcs-py`)。

(funcs-py)=

```{literalinclude} funcs.py
:caption: funcs.py
```

このコードを実行すると、以下のように関数名の一覧が取得できます({numref}`exec-funcs-py`)。

(exec-funcs-py)=

```{code-block} bash
:caption: "スクレイピングを実行"

(env) $ python funcs.py
件数: 52
abs
aiter
all
anext
any
ascii
bin
breakpoint
:
```

```{index} PEP8
```

```{admonition} コラム: Pythonのコーディング規約「PEP8」
Pythonには [PEP8（ペップエイト）](https://peps.python.org/pep-0008/) というコーディング規約があります。
チームで開発をする際、人によってプログラムコードの書き方がバラバラだと読みにくいコードになってしまいます。
そのため、PEP8のルールに従う習慣を身につけておくとよいでしょう。

コードがPEP8のルールに従っているかは、 [pycodestyle](https://pep8.readthedocs.io/en/latest/) というツールで検証できます(以前はツールの名前もpep8でした)。

pycodestyleは `pip install pycodestyle` でインストールして使用します。
`funcs.py` を検証するには、 `pycodestyle funcs.py` を実行します。
```

### コードの解説

上記のコードがどういった内容なのかを解説します。

- 以下のコードはRequestsとBeautiful Soup 4をimportして利用できるようにしています。

```{literalinclude} funcs.py
:caption: モジュールのimport
:lines: 1-2
```

- メインとなる処理を `main` 関数として定義しています。
  なお、関数の名前に特に決まりはなく、必ずしも `main` である必要はありません。

```{literalinclude} funcs.py
:caption: main()関数の定義
:lines: 5
```

- Requestsを使用して、Webページの内容(HTML)を取得します。res.contentにHTMLの中身が文字列データとして入っています。

```{literalinclude} funcs.py
:caption: ページの内容を取得
:lines: 6-8
```

- 次にHTMLをBeautiful Soup 4に渡して解析します。HTMLの解析についてはもう少し詳しく説明します。

```{literalinclude} funcs.py
:caption: WebページをBeautiful Soup 4で解析
:lines: 9-18
```

- 最後に、このスクリプトが実行された時に、main()関数を実行するように指定します。

```{literalinclude} funcs.py
:caption: main()関数を実行
:lines: 21-22
```

### HTMLの解析の解説

Beautiful Soup 4でHTMLを解析して、値が取り出せましたが、どのように指定しているのでしょうか?
組み込み関数一覧のHTMLを見てみると、以下のような形式になっています。({numref}`sponsor-list-html`)

(sponsor-list-html)=

```{code-block} html
:caption: "組み込み関数一覧のHTML"
:emphasize-lines: 1,3,19,21

<dl class="py function">
    <dt id="abs">
        <code class="sig-name descname">abs</code>
        <span class="sig-paren">(</span><em class="sig-param">
        <span class="n">x</span></em>
        <span class="sig-paren">)</span>
        <a class="headerlink" href="#abs" title="この定義へのパーマリンク">¶</a>
    </dt>
    <dd>
        <p>数の絶対値を返します。引数は整数、浮動小数点数または
            <code class="xref py py-meth docutils literal notranslate">
                <span class="pre">__abs__()</span>
            </code>
            が実装されたオブジェクトです。引数が複素数なら、その絶対値 (magnitude) が返されます。
        </p>
    </dd>
</dl>

<dl class="py function">
    <dt id="aiter">
        <code class="sig-name descname">aiter</code>
        <span class="sig-paren">(</span>
        <em class="sig-param">
            <span class="n">async_iterable</span>
        </em>
        <span class="sig-paren">)</span>
        <a class="headerlink" href="#aiter" title="この定義へのパーマリンク">¶</a>
    </dt>
    <dd>
        <p>:term:
            <a href="#id1">
                <span class="problematic" id="id2">`</span>
            </a>
            asynchronous iterable`から :term:
            <a href="#id3">
                <span class="problematic" id="id4">`</span>
            </a>
            asynchronous iterator`を返します。
            <a href="#id5">
                <span class="problematic" id="id6">``</span>
            </a>x.__aiter__()``を呼び出すのと等価です。
        </p>
        <p>なお、:func:
            <a href="#id1">
                <span class="problematic" id="id2">`</span>
            </a>
            iter`とは異なり、:func:
            <a href="#id3">
                <span class="problematic" id="id4">`</span>
            </a>
            aiter`は第二引数を持ちません。
        </p>
        <div class="versionadded">
            <p>
                <span class="versionmodified added">バージョン 3.10 で追加.</span>
            </p>
        </div>
    </dd>
</dl>
(以下続く)
```

このHTMLを見ると、関数の名前とURLは以下のようにして取得できそうです。

- 1つの関数の情報は `<dl class="py function">` の中に入っている
- 関数名は `<code class="sig-name descname">` タグで囲まれた中に入っている

HTMLの構造がわかったところで、もう一度HTMLを解析しているコードを見てみます。

```{index} html.parser
```

```{literalinclude} funcs.py
:caption: WebページをBeautiful Soup 4で解析
:lines: 9-18
```

まず、 `soup.find_all()` メソッドで、全関数の情報が含まれている dl 要素を取得しています。
次に、各関数情報(func変数に入っている)から値を取り出しています。
関数名を取得して、出力しています。

## 作り変えてみよう

RequestsやBeautiful Soup 4の動作を変えて、さまざまなWebページから色んな要素を取得できます。

以下にそれぞれのライブラリの簡単な使い方を紹介します。それ以外にもいろいろな使用方法があるので、ドキュメントを参考にしていろいろ作り変えてみてください。

```{index} Requests
```

### Requests の主な使い方

ここでは Requests の主な使い方の例をいくつか載せます。
詳細については以下の公式ドキュメントを参照してください。

* **公式ドキュメント**: [Requests: HTTP for Humans](https://requests.readthedocs.io/)

以下は認証つきのURLにアクセスして、結果を取得する例です。

```{code-block} pycon
:caption: "認証付きURLにアクセスする"

>>> import requests
>>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
>>> r.status_code
200
```

```{index} Requests single: Requests; POST
```

POST を行う場合は以下のように、POSTのパラメーターを辞書で定義します。

```{code-block} pycon
:caption: "requests で POST する"

>>> payload = {'key1': 'value1', 'key2': 'value2'} # POST するパラメーター
>>> r = requests.post('http://httpbin.org/post', data=payload)
>>> print(r.text)
```

```{index} Requests single: Requests; GET
```

GET に `?key1=value1&key2=value2` のようなパラメーター付きでアクセスする場合も同様に、辞書で定義します。

```{code-block} pycon
:caption: "requests でパラメーター付で GET する"

>>> payload = {'key1': 'value1', 'key2': 'value2'}
>>> r = requests.get('http://httpbin.org/get', params=payload)
>>> print(r.url)
http://httpbin.org/get?key2=value2&key1=value1
>>> payload = {'key1': 'value1', 'key2': ['value2', 'value3']}
>>> r = requests.get('http://httpbin.org/get', params=payload)
>>> print(r.url)
http://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

### Beautiful Soup 4の主な使い方

ここではBeautiful Soup 4の主な使い方の例をいくつか載せます。
詳細については以下の公式ドキュメントを参照してください。

* **公式ドキュメント**: [Beautiful Soup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)

```{index} Beautiful Soup 4 single: Beautiful Soup 4; Documentation
```

```{code-block} pycon
:caption: "Beautiful Soup 4の使用例"

>>> import requests
>>> from bs4 import BeautifulSoup
>>> r = requests.get('https://www.python.org/blogs/')
>>> soup = BeautifulSoup(r.content, 'html.parser') # 取得したHTMLを解析
>>> soup.title # titleタグの情報を取得
<title>Our Blogs | Python.org</title>
>>> soup.title.name
'title'
>>> soup.title.string # titleタグの文字列を取得
'Our Blogs | Python.org'
>>> soup.a
<a href="#content" title="Skip to content">Skip to content</a>
>>> len(soup.find_all('a')) # 全ての a タグを取得しt len() で件数を取得
164

     url = 'https://www.python.org/news/'
     res = requests.get(url)
     soup = BeautifulSoup(res.content, 'html.parser')
```

```{index} find/find_all single: Beautiful Soup 4; find() single: Beautiful Soup 4; find_all()
```

また、 `find()` `find_all()` などでタグを探す場合には、タグの属性などを条件として指定できます。

```{code-block} pycon
:caption: "find/find_all の使用例"

>>> len(soup.find_all('h1')) # 指定したタグを検索
3
>>> len(soup.find_all(['h1', 'h2', 'h3'])) # 複数のタグのいずれかにマッチ
24
>>> len(soup.find_all('h3', {'class': 'event-title'})) # <h3 class="event-title"> にマッチ
5
```

## まとめ

本節では、Pythonでスクレイピングをする方法を解説しました。

RequestsとBeautiful Soup 4を使いこなすことにより、さまざまなウェブサイトから情報を取得できるようになります。

なお、短時間にWebサイトに大量にアクセスをすると迷惑となるので、そういうことがないようにプログラムを実行するときには注意してください。

## 参考書籍

Pythonでのスクレイピングについてもいくつか書籍が出ています。

- [PythonによるWebスクレイピング](https://www.oreilly.co.jp/books/9784873117614/)
- [Pythonクローリング＆スクレイピング ―データ収集・解析のための実践開発ガイド](https://gihyo.jp/book/2017/978-4-7741-8367-1)
- [Pythonによるスクレイピング＆機械学習 開発テクニックBeautifulSoup、scikit-learn、TensorFlowを使ってみよう](https://www.socym.co.jp/book/1079)
- [Pythonエンジニア ファーストブック](https://gihyo.jp/book/2017/978-4-7741-9222-2) (第4章 PythonによるWebスクレイピング)
