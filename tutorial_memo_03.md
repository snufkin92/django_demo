# はじめての Django アプリ作成、その 3

## 概要
* 動的な画面の作り方のチュートリアル。
* 流れとしては  
  - view.py に1画面に相当する関数を記述。
  - アプリケーション（今回はpolls）配下の urls.py にview.pyで作成した関数を紐付ける。
* Pollsアプリケーションでは  以下の４画面を作る。
  - 質問（index）-- 最新の質問をいくつか表示
  - 詳細（detail）-- 結果を表示せず、質問テキストと投票フォームを表示
  - 結果（results）-- 特定の質問の結果を表示
  - 投票（vote）-- 特定の質問の選択を投票として受付


## 例）投票（vote）画面
polls/views.py に　question_id(Questionオブジェクトを一意に特定するid）を表示する関数を定義
```
def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

polls/urls.py で URLと業務ロジック（vote関数）を結びつける。  

下記のコードは  

polls/{question_id}/vote というURLでアクセスされた場合  

 > viewsモジュールのvote関数を呼び出す  
 
という意味。

name属性の役割は、テンプレート作成時に分かります。

```
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
```

下記は、データベースにアクセスするコード無しに、pools_question テーブル から  
pub_date(投票日) の降順にソートされた先頭５レコードを取得し、listとして格納している。
```
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
```
    

## templateディレクトリ
djangoでは暗黙的なルールとして、「template」という名前のディレクトリがあると、そこに画面テンプレートが存在していると解釈する。

この設定を変えたい場合は、mysite/settings.py の TEMPLATES => APP_DIRS　を設定する。

index.htmlを作る場合、今回は以下のような階層になる。
```
プロジェクト名/
    manage.py
    mysite/
    polls/
        template/
            polls/
                index.html
```

### 画面のテンプレート（index.html）
{% %} で囲ってある部分が、django が 解読する場所。

{% if latest_question_list %}  
　：　”latest_question_list”という変数が存在した場合のみ続行

{% for question in latest_question_list %}  
　：　latest_question_list の中からquestion名前で要素を取り出しループ処理

```
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    {% if latest_question_list %}
    <ul>
        {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
        {% endfor %}
    </ul>
    {% else %}
    <p>No polls are available.</p>
    {% endif %}
</body>
</html>
```

```
python manage.py runserver
```
でwebサーバを起動し、http://127.0.0.1:8000/polls にアクセス。

## テンプレート内のハードコードされたURLを削除


```
# 悪い例）
 <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```
この書き方の悪いところは、hrefの箇所（リンク先のURLをそのまま記述している）。

```
# 良い例）
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

urls.pyに書いた name='detail' と対応づける事で、URLが変更されても、変更箇所はurls.pyだけを変更すれば良くなる。