# 現場Rails Chapter3 メモ

・アプリ作成にあたって、一般的に次のような準備を行う。
①作成するアプリの内容を考える・・・タスク管理のためのアプリ
②アプリの名前を考える ・・・taskleaf
③アプリの雛形を作成する・・・rails _5.2.1_ new taskleaf -d postgresql (インストールされているRailsの最新バージョンが優先されるためバージョン指定をする場合はこのように書く。)
④データベースを作成する

・今回は次のような準備をプラスで行う
①ビュー層を効率よく書くためにSlimを使えるようにする
②アプリケーションの見栄えを良くするためにBoostrapを導入する
③Railsのエラーメッセージなどを日本語で出せるようにする

・データベースの環境ごとの使い分けがある。アプリケーションが完成して本当に利用し始めたことで作られるデータと、開発の時に動作確認のために作るデータとは性質が違う。同じところに入れてしまうと、テスト用のデータが邪魔になったりする。自動テストのためのデータを格納しておくべき場所も他の用途のデータが混じり込まないように専用の場所にしたい。

development・・・開発時の動作確認を行う
test・・・自動テストを行う
production・・・ユーザーが利用可能な形で稼働させる

・railsのバージョンはrails newの時に指定してもgemfileをskipしないと指定したバージョンでインストールされない。

・Railsを使用した開発では、テンプレートエンジンを使用する。テンプレートエンジンを使用すると、アプリケーションの画面を、HTMLの構造が直感的にわかりやすくテンプレート形式で書くことができます。
テンプレートエンジンは、HTMLのテンプレート（雛形）とそこに記述された動的な処理から、最終的なHTMLを生成するための仕組み。

・現場ではHTMLをツリー構造として簡潔に表現できるHamlやSlimといった別のテンプレートエンジンが利用されることが多い。
タグの開始と終了を両方記述しなければならないERBに比べて、インデントでツリー構造を表現しているHamlやSlimは簡潔で読みやすい。

・Slimを利用するときは２つのgemをインストールする必要がある。１つはSlimのジェネレータを提供してくれる　Slimーrailsというgem。もう１つは、ERB形式のファイルをslim形式に変換してくれるerb2slimコマンドを提供してくれるhtml2slimというgemである。

・Rails のエラーメッセージはデフォルトでは英語である。英語以外の言語を使用したいときは、言語ごとの翻訳を追加して使用する必要がある。ダウンロードしたファイルを適用させたい場合は、config/locales/ja.ymlに記述し後に、config/initializers/locale.rbというファイルを作成して、Rails.application.config.i18n.default_locale = :ja と記述する。これらはRailsアプリで複数の言語を併用する仕組みである。

・キャメルケース・・・ラクダのコブ HogePoge

・bin/rails g model [モデル名] [属性名: データ型…] [オプション]はモデルクラスのファイル、マイグレーションファイル、モデルの自動テストのファイルの雛形が自動的に作成される。

・リクエストパラメータの送り方は、HTTPの仕組みの上で、２通りにわかれる。
1. POSTで送る。基本的にはHTMLのform要素をsubmitすることで送られる。
2. GETで送る。基本的にはURLの?以降の情報を含めることで送る。普通に?のついたURLにブラウザからアクセスするほか、form要素のmethod属性にgetを指定して、form要素のsubmitを通じてそのようなリクエストを送ることもできる。
コントローラにおいてparamsというメソッドを利用することで、POSTかGETかに関係なく、送られたリクエストパラメータをHashのような感覚で取得することができる。また「送られてきたURL中に含まれる、routes.rbで定義された可変部分」もリクエストパラメータとして利用することができる。task/:idというURLの定義がroutes.rbに存在する場合、task/7というURLはこれにマッチし、:idに対応する7という値をparams[:id]で取得することができる。

・link_toは、リンクの中身をブロックで囲むことができます。

・link_toでは下記の様にHTTPメソッドを指定することができます。
<%= link_to {テキスト}, {URL}, method: :delete %>
rails-ujsのおかげで擬似的にDELETEメソッドでのリクエストになります。

・link_to によって delete メソッドなどをaタグで実装できているように感じますが、実際にはrails-ujsが動的にformを組み立てて送信しているという動作になります。

・usersテーブルの全てのレコードのidカラムの情報を取得したい場合はUser.all.pluck(:id)とすれば良い
pluckメソッドを使うことで特定のカラムを取ってくることができます。
例えばUser.all.pluck(:id)とすると
SELECT `users`.`id` FROM `users`
といったSQLが発行され、userの他の余計なカラムを取得せず、[1,2,3,4,5]の様にidのみの配列を返します。 よって、特定のカラムのみほしい場合はpluckを使うとパフォーマンスが良いです。

・パフォーマンス的な観点でいうと、データが存在するかどうかのみを確認したい場合は、User.all.present?よりUser.all.exists?を使う方が良い
データが存在するかどうかのみを確認したい場合はexists?を使う方がパフォーマンスが良いです。
present?とexists?を使った場合に発行されるSQLは下記の様になります。
User.all.present?
#=> `SELECT users.* FROM users`

User.all.exists?
#=> `SELECT 1 AS one FROM users LIMIT 1`
present?の場合は、一旦全てのデータを取ってきて、その後present?メソッドによってデータが存在するか確認しています。
exists?の場合は、取得するデータ数を1に制限（LIMIT 1）しており、かつレコードが存在しているかだけ（つまりカラムの値は取っていない）を確認しているためパフォーマンスが良いです。
ただ、データが存在するかどうかだけではなく、そのデータの内容も別の場所で使う場合はpresent?を使う形で問題ありません。


・selectメソッドでは、取ってくるカラムを指定して、欲しい値のみ取ってくることができる。
selectメソッドでは、取ってくるカラムを指定して、欲しい値のみ取ってくることができます。
例えば、Userのidのみを取ってきたい場合は
User.all.select('id')
とすれば、idのみを取得することができます。
User.all.pluck(:id)
との違いは、pluckの場合はidの配列が返ってくるのに対して、selectはActiveRecordのオブジェクトとして返ってきます。