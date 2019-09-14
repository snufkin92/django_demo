# はじめての Django アプリ作成、その 6
https://docs.djangoproject.com/ja/2.2/intro/tutorial06/

## 概要
- 静的（static）ファイル関連の設定
    - アプリの構造をカスタマイズする
    - スタイルシート（CSSファイル）と画像ファイルをWeb投票アプリケーションに追加する

静的（static）ファイルとは、CSSファイルやJavaScriptファイル、画像ファイルなどのように、
リクエストに応じて中身を変更せずにそのまま配信可能なファイル

CSSファイル：https://techacademy.jp/magazine/4857

 ## アプリの構造をカスタマイズする
- pollsディレクトリの中にstaticディレクトリを作成
- そのstaticディレクトリの中にpollsという新しいディレクトリを作り、さらにその中に、
　style.cssという名前のファイルを作成
- デフォルトのファインダーであるAppDirectoriesFinderの仕組みのおかげでpolls/style.css
　と書くだけでこの静的ファイルを参照可能

polls/staticの中に静的ファイルを直接置かないのは、テンプレートファイルを作成した場合と
同様で、もし異なるアプリケーションの中に同じ名前の静的ファイルが場合に、Djangoが区別
できなくなることを防ぐため

## スタイルシートの追加
style.cssに以下のコードを配置（polls/static/polls/style.css）
```
li a {
    color: green;
}
```

次に、polls/templates/polls/index.htmlの上部に以下を追加
```
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```

以上でhttp://localhost:8000/polls/ をリロードすると質問のリンクが緑色になる

## 背景画像の追加
画像のためのimagesサブディレクトリをpolls/static/polls/ ディレクトリの中に作成。この中に
background.gif という名前の画像を配置。
さらにスタイルシート（polls/static/polls/style.css）に以下のコードを追加

```
body {
    background: white url("images/background.gif") no-repeat;
}
```

http://localhost:8000/polls/ をリロードすると、読み込んだ背景画像がスクリーンの左上部に確認できる