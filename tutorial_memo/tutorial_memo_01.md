# はじめての Django アプリ作成、その 1

## チュートリアル全般
* チュートリアルは Django 2.2 と Python 3.5が前提。
（上述以上のバージョンが入っていれば大丈夫なはず）
* [django-usersメーリングリスト](https://docs.djangoproject.com/ja/2.2/internals/mailing-lists/#django-users-mailing-list)


## プロジェクトを作成する
djangoを使う場合はコードを置きたい場所に移動してから下記のコマンドで開発のテンプレートを作成することから始まる。

```django-admin startproject プロジェクト名```

pycharmを使う場合は、pycharmで作成したプロジェクトは以下でコマンドを打てば良い。

```
プロジェクト名/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

といった階層が作成される。とりわけ重要なのは
* settings.py:データベースへの接続、文字コードなどを設定する（[Djangoの設定](https://docs.djangoproject.com/ja/2.2/topics/settings/))

## サーバの起動
djangoにはwebサーバが内蔵されており、下記のコマンドで起動する。
（pythonのコードを変更したり、追加した場合は偶に再起動が必要）

参考:[インターネットの仕組み](https://tutorial.djangogirls.org/ja/how_the_internet_works/)

```python manage.py runserver```


```
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
の意味は、
IPアドレス127.0.0.1(localhost)に、ポート番号8000でアクセス
できるwebサーバが起動したという意味。

## Polls アプリケーションをつくる
チュートリアルでは投票アプリを作成していく。

Pullsアプリケーションはどこに作成しても良いが、チュートリアルでは作成したプロジェクト(mysite)
直下に作成する。

```python manage.py startapp polls```

startprojectでdjangoが動く環境を作り、startappでアプリケーションのテンプレートを作成している。
作成されたファイルにはそれぞれ、役割があり

polls/views.py：各画面に返却する情報を関数として定義する。

polls/urls.py：views.pyの関数とurlとを紐付ける。

## はじめてのビュー作成

polls/views.py
```
def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

polls/urls.py
```
urlpatterns = [

　　# http://localhost:8000/polls/ にアクセスされたら、views.index関数を呼び出す。
　　# name='index'は URLに対する名前付け
    path('', views.index, name='index'),
]

```

mysite/urls.py
アプリケーションをdjangoの管理下に置く。
```
urlpatterns = [
 　　# urlに'polls/'が入っていたら、polls.urlsに処理を移譲する
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

## アプリケーションの動作確認
サーバを起動し、下記URLにアクセス

http://localhost:8000/polls/
