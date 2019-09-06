# はじめての Django アプリ作成、その 5 [自動テスト]
https://docs.djangoproject.com/ja/2.2/intro/tutorial05/

## 概要
- 作業の効率化のため、またコードの質を高めるため、自動テストを導入する
- Django には自動テストを容易にする機能が備わっている
- テストコードはまずは質や量を気にするよりもどんどん書くべき

## 自動テストとは
- 自動テスト: コードの動作を確認する単純なプログラム
  - システムが実行することで手動でテストをする時間を削減できる
  - テストを書くことでその処理の目的や仕様が明確になる
    - テストコードがないだけでNGのケースもある
    - 他のメンバーとの共同作業で役立つ
  - テストの粒度はメソッド単位からソフトウェア全体の動作単位まで様々
- テストを書くアプローチは多種多様
  - テスト駆動開発: コードを書く前にテストを書く = 問題を言語化してそれを解決するためのコードを書く
  - 基本的にテストコードは1度作ればずっと動き続けるもの、想定されるケースが多く網羅されているほど良い
  - テストコードを作るべき機能やタイミングに迷ったら今から行う作業対象に対して作成してみよう
- テストコードを整理しておくための良いルールも知られている
  - テスト対象のクラスや条件に合わせて分割
  - テストメソッドには説明的な名前をつける

## ケーススタディ
polls アプリケーションの `Question.was_published_recently()` メソッドに関して検討する。仕様として下記が想定される。  

  - 1日より前に公開された場合: False
  - 1日内に公開された場合: True
  - 未来に公開予定の場合: False
  - 日付指定がなかった場合や不適切な値の場合: エラー

手動でテストをすると都度下記のようなコマンドを入力する必要がある。  

``` bash
> python manage.py shell

>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>>
>>> # 公開日が1日と1秒前ならFalseのテスト
>>> Question(pub_date=timezone.now() - datetime.timedelta(days=1, seconds=1)).was_published_recently()
False # OK
>>>
>>> # 公開日が23時間59分59秒前ならTrueのテスト
>>> Question(pub_date=timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)).was_published_recently()
True # OK
>>>
>>> # 公開日が30日後ならFalseのテスト
>>> Question(pub_date=timezone.now() + datetime.timedelta(days=30)).was_published_recently()
True # NG
>>>
>>> # 公開日がNoneならエラーのテスト
>>> Question(pub_date=None).was_published_recently()
Traceback (most recent call last):... # OK
```

一方で自動テストを作成した場合、下記のコマンドを入力するだけでテスト結果が確認できるようになる。

``` bash
> python manage.py test polls
```

当然 `Question.was_published_recently()` メソッド以外もテストをする場合、手動の場合は都度同じ労力が必要となるが、自動テストの場合は最初にコードを書いてしまえば後は少ない労力で確認が可能となる。

## django におけるメソッドの自動テスト
1. test で始まる名前のファイル(例えば tests.py, test_hoge.py)に下記のようなコードを記述していく
  ``` python
  from django.test import TestCase
  class HogeTest(TestCase):  # django.test.TestCase クラスのサブクラスが自動テストとして認識される
      def test_<methodname1>_<condition1>(self):  # test で始まるメソッドが自動テストとして認識される
        actual = <テスト対象の実行結果>
        expected = <期待される結果>
        self.assertIs(actual, expected)  # 実行結果と期待結果が一致しているかが検証される
      def test_<methodname1>_<condition2>(self):
        ...
      def test_<methodname2>_<condition3>(self):
        ...
  ```
2. `python manage.py test <app名>` を実行する
3. 自動テストが全てOKになるまでバグを修正する
4. 改善に繋げていく
   - テストケースを増やす、ビューレベルのテスト追加するなど包括的なテストを行う
   - リファクタリングを行う
   - より良いサービスになるように機能追加や改修を行う

## その他テスト周りで聞く用語
- 継続的インテグレーション: 自動化により品質向上や納期短縮を目指す手法、一例としてコミットなど特定のタイミングで自動で自動テストを実行する手法がある
- Selenium: 実際にブラウザを立ち上げて自動テストを行うフレームワーク
- コードカバレッジ: プロダクトコードのうちテストが行われた割合

