# はじめての Django アプリ作成、その 4
## 概要
- フォーム処理
    - HTML の \<form\> 要素を入れる
    - 送信されたデータを処理するビューの作成
    - vote()関数の実装
    - GET データ/ POSTデータの扱い
    - 投票後、結果ページへリダイレクトするビューの作成
- コードを簡潔にする
     - URLconf の修正
     - 古い不要なビューの削除
     - Django の汎用ビューを使用
     
 ## 投票フォームの作成
- detail.htmlに\<form\>要素を入れる
    - 質問の選択肢をラジオボタンで選ぶフォームを作成
    - 選択肢の送信はGETではなくPOSTを使う
    - 各選択肢はfor文を使って表示
    - クロス サイトリクエストフォージェリ対策として{% csrf_token %} テンプレートタグを使用

- vote() 関数の実装
    -  request.POST のオブジェクトを使う。request.POST['choice'] の形で使い、この場合は選択された選択肢の ID が文字列として返却される。
    -  choice がない場合にはエラーメッセージ付きの質問フォームを再表示するように実装。
    - ? HttpResponseRedirect と HttpResponseの違い
    -  reverse() 関数を使い、リダイレクト先のURLの文字列を生成

- 質問の結果ページにリダイレクトするビューの作成  
- 投票結果表示テンプレートの作成

## コードの簡潔化
Djangoには、パターンを抽象化した汎用ビューがある。書くPythonコードを減らせる。  
簡潔ステップ化するためのステップは以下の通り
#### URLconf の修正
- detail,resultsのURLパスの文字列は`<pk>`とする
#### views の修正
- index 、 detail 、と results のビューを使うのをやめ、Django の汎用ビューを使う
- ListView「オブジェクトのリストを表示する」
- DetailView「あるタイプのオブジェクトの詳細ページを表示する」
    - "pk" という名前で URL からプライマリキーをキャプチャして渡す
    - デフォルトでは<app name>/<model name>_detail.html という名前のテンプレートを使う
    - 今回はtemplate_nameにて既存のテンプレートを使用するよう指定
- コンテキスト変数は自動的に生成される
    - DetailViewではquestion 
    - ListViewではquestion_list 
    - context_object_name属性で任意に設定できる  
    例　`context_object_name = 'latest_question_list'`
 
 #### 投票アプリケーションを触ってみましょう！