# はじめての Django アプリ作成、その2
## Database の設定
mysite/settings.py　について
- Djangoの設定を定義
- デフォルトで使用するDBはSQLite
- （頭痛の種となるデータベースの移行作業 ...えっ？？？頭痛になるくらいならDB移行しなければいいじゃないですか（＾m＾）冗談)
- `TIME_ZONE = 'Asia/Tokyo'`　←TIME_ZONEを修正
Django は、Windows 環境で代替タイムゾーンを正しく扱うことができない。Django を Windows で実行している場合、TIME_ZONE はシステムのタイムゾーンに合わせてセットする必要あり。
- INSTALLED_APPS  は今回はデフォルトのまま使用。migrateした時に標準のテーブルが作成される。

```python manage.py migrate
python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK

```

### Djangoが作成したテーブルを表示

``` >sqlite3 db.sqlite3
>sqlite3 db.sqlite3
SQLite version 3.29.0 2019-07-10 17:32:03
Enter ".help" for usage hints.
sqlite> .schema
CREATE TABLE IF NOT EXISTS "django_migrations" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "app" varchar(255) NOT NULL, "name" varchar(255) NOT NULL, "applied" datetime NOT NULL);
CREATE TABLE sqlite_sequence(name,seq);
CREATE TABLE IF NOT EXISTS "auth_group_permissions" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "group_id" integer NOT NULL REFERENCES "auth_group" ("id") DEFERRABLE INITIALLY DEFERRED, "permission_id" integer NOT NULL REFERENCES "auth_permission" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE TABLE IF NOT EXISTS "auth_user_groups" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "user_id" integer NOT NULL REFERENCES "auth_user" ("id") DEFERRABLE INITIALLY DEFERRED, "group_id" integer NOT NULL REFERENCES "auth_group" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE TABLE IF NOT EXISTS "auth_user_user_permissions" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "user_id" integer NOT NULL REFERENCES "auth_user" ("id") DEFERRABLE INITIALLY DEFERRED, "permission_id" integer NOT NULL REFERENCES "auth_permission" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE UNIQUE INDEX "auth_group_permissions_group_id_permission_id_0cd325b0_uniq" ON "auth_group_permissions" ("group_id", "permission_id");
CREATE INDEX "auth_group_permissions_group_id_b120cbf9" ON "auth_group_permissions" ("group_id");
CREATE INDEX "auth_group_permissions_permission_id_84c5c92e" ON "auth_group_permissions" ("permission_id");
CREATE UNIQUE INDEX "auth_user_groups_user_id_group_id_94350c0c_uniq" ON "auth_user_groups" ("user_id", "group_id");
CREATE INDEX "auth_user_groups_user_id_6a12ed8b" ON "auth_user_groups" ("user_id");
CREATE INDEX "auth_user_groups_group_id_97559544" ON "auth_user_groups" ("group_id");
CREATE UNIQUE INDEX "auth_user_user_permissions_user_id_permission_id_14a6b632_uniq" ON "auth_user_user_permissions" ("user_id","permission_id");
CREATE INDEX "auth_user_user_permissions_user_id_a95ead1b" ON "auth_user_user_permissions" ("user_id");
CREATE INDEX "auth_user_user_permissions_permission_id_1fbb5f2c" ON "auth_user_user_permissions" ("permission_id");
CREATE TABLE IF NOT EXISTS "django_admin_log" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "action_time" datetime NOT NULL,"object_id" text NULL, "object_repr" varchar(200) NOT NULL, "change_message" text NOT NULL, "content_type_id" integer NULL REFERENCES "django_content_type" ("id") DEFERRABLE INITIALLY DEFERRED, "user_id" integer NOT NULL REFERENCES "auth_user" ("id") DEFERRABLE INITIALLY DEFERRED, "action_flag" smallint unsigned NOT NULL CHECK ("action_flag" >= 0));
CREATE INDEX "django_admin_log_content_type_id_c4bce8eb" ON "django_admin_log" ("content_type_id");
CREATE INDEX "django_admin_log_user_id_c564eba6" ON "django_admin_log" ("user_id");
CREATE TABLE IF NOT EXISTS "django_content_type" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "app_label" varchar(100) NOT NULL, "model" varchar(100) NOT NULL);
CREATE UNIQUE INDEX "django_content_type_app_label_model_76bd3d3b_uniq" ON "django_content_type" ("app_label", "model");
CREATE TABLE IF NOT EXISTS "auth_permission" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "content_type_id" integer NOT NULL REFERENCES "django_content_type" ("id") DEFERRABLE INITIALLY DEFERRED, "codename" varchar(100) NOT NULL, "name" varchar(255) NOT NULL);
CREATE UNIQUE INDEX "auth_permission_content_type_id_codename_01ab375a_uniq" ON "auth_permission" ("content_type_id", "codename");
CREATE INDEX "auth_permission_content_type_id_2f476e4b" ON "auth_permission" ("content_type_id");
CREATE TABLE IF NOT EXISTS "auth_user" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "password" varchar(128) NOT NULL, "last_login" datetime NULL, "is_superuser" bool NOT NULL, "username" varchar(150) NOT NULL UNIQUE, "first_name" varchar(30) NOT NULL, "email" varchar(254) NOT NULL, "is_staff" bool NOT NULL, "is_active" bool NOT NULL, "date_joined" datetime NOT NULL, "last_name" varchar(150) NOT NULL);
CREATE TABLE IF NOT EXISTS "auth_group" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "name" varchar(150) NOT NULL UNIQUE);
CREATE TABLE IF NOT EXISTS "django_session" ("session_key" varchar(40) NOT NULL PRIMARY KEY, "session_data" text NOT NULL, "expire_date" datetime NOT NULL);
CREATE INDEX "django_session_expire_date_a5c62663" ON "django_session" ("expire_date");
sqlite>
``` 

## モデルの作成
- モデルとは、データベースのレイアウトと、それに付随するメタデータ。
- 設計思想について
　Ruby On Rails をよくわかっていないので、設計思想の違いについて詳しい方いらっしゃったら教えてください。

###  投票項目 (Question) と選択肢 (Choice) の二つのモデルを作成
- 各モデルは一つのクラスで表現され、いずれもdjango.db.models.Model のサブクラス
- 各モデルには複数のクラス変数があり、個々のクラス変数はモデルのデータベースフィールドを表現
- 各フィールドにどのようなデータ型を記憶させるか を定義
- フィールド名はPythonコード、データベースも列の名前として使用可能
- CharField には max_length を指定する必要あり
- フィールドのdefault 値の定義も可能

## モデルを有効にする
- アプリケーションをプロジェクトに含める
  INSTALLED_APPSに 'polls.apps.PollsConfig'を 追加
- マイグレーションとはDjangoがモデル(データベーススキーマ)の変更を保存する方法
```python manage.py makemigrations polls
> python manage.py makemigrations polls
Migrations for 'polls':
  polls\migrations\0001_initial.py
    - Create model Question
    - Create model Choice
```

- sqlmigrate コマンドでDjangoが必要なSQLが何であるかを表示
``` python manage.py sqlmigrate polls 0001
> python manage.py sqlmigrate polls 0001
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "question_text"　varchar(200) NOT NULL, "pub_date"
 datetime NOT NULL);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, "votes" intege
r NOT NULL, "question_id" integer NOT NULL REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```
- データベースに変更を適用する。
```python manage.py migrate
>python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions Running migrations:
  Applying polls.0001_initial... OK
```
## API で遊んでみる

- テーブルの操作をPython Shellで実施するイメージ
- `python manage.py shell` で、`DJANGO_SETTINGS_MODULE` 環境変数を設定
settings.py ファイルへの import パスが与えられる

- polls/models.py ファイル内の Question モデルを編集
` __str__()` メソッドとは？ 
文字列を返してあげる必要あり。でもなぜ、メソッドの名前を`__str__()`とするのか？
```
>>> from polls.models import Choice, Question  # 先ほど作成したモデルをインポート

# 最初は"Question"　には何もセットされていない
>>> Question.objects.all()
<QuerySet []>

# "Question" の作成
# datetime.datetime.now() の代わりにtimezone.now（）を使用
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# オブジェクトをデータベースに保存
>>> q.save()

# IDが振られます
>>> q.id
1

# Pythonからモデル（データ）にアクセス可能
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# 値の変更、保存
>>> q.question_text = "What's up?"
>>> q.save()

# 全ての"Question"を表示
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

- `<Question:  Question  object  (1)>` は、このオブジェクトの表現として役に立たない
 `polls/models.py`  内の`Question` モデルに`__str__()`	 メソッドを `Question` と `Choice` の両方に追加
 - Python メソッドを追加している（Python メソッドが追加できる）
 - `was_published_recently`メソッドも追加して、もう一度Python Shellの実行

```
>>> from polls.models import Choice, Question

# 作成した __str__() メソッドの確認
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# APIではidやキーワードでも検索可能
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# 今年公開されたQuestionも検索できる
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# 検索にマッチしない、データがない場合以下のメッセージが出力される
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# プライマリーキーで検索をすることも可能
>>> Question.objects.get(pk=1)
<Question: What's up?>

# 作成したメソッドを使うことができる
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Question に対応する Choices　を作成する。INSERT ステートメントを使う。
>>> q = Question.objects.get(pk=1)

# 現在のchoiceを表示
>>> q.choice_set.all()
<QuerySet []>

# 3つの選択肢を作成.
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# 対応する質問を表示
>>> c.question
<Question: What's up?>

# 逆に、questionオブジェクトからChoiceオブジェクトにアクセス
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# ？？？The API automatically follows relationships as far as you need.
# ？？？Use double underscores to separate relationships.
# ？？？This works as many levels deep as you want; there's no limit.
# ？？？APIは必要な限り関係を自動的に追跡します。
# ？？？二重アンダースコアを使用して関係を分離します。
# ？？？これは、必要なだけ深いレベルで機能します。 制限はありません。

# 先ほど作成した「current_year」変数を再利用してpub_dateが今年の質問のすべての選択肢を検索
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# 選択肢の削除には、delete() を使う
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()

```
## Django Adminの紹介
###  管理ユーザーを作成する
（チュートリアルにて補足することがないので省略）
- Djangoの認証フレームワーク django.contrib.auth にてgroups と users が作られる

### Poll アプリを admin 上で編集できるようにする
polls/admin.pyを下記の通り修正
``` polls/admin.py
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```
- `polls/admin.py`を修正して保存すると即時反映っぽい。
- フォームは Question モデルから生成される
- Choose a Time の画面がちょっと気が利く。新鮮。（個人の意見）