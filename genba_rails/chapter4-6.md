# 現場Rails Chapter4-6

・データ絞り込みには次の3点を意識すること。

①起点・・・検索や更新などのコードを書き出すスタート地点。基本的には、処理対象のモデルのクラスが起点となる。そのほか、has_many関連づけをした場合、例えばuser.tasksがある。この場合Taskクラスを起点としてwhere(user_id : user.id )となる。

②絞り込み条件・・・起点に対して絞り込みの条件を追加していく部分。①②を記述してもまだ検索などの処理は実行されない。実行部分にあたる③を呼び込むことではじめて実行されるるまたto_sqlというメソッドを使って、生成予定のSQL見ることができる。

③実行部分

・scope・・・クエリー用のメソッドの連続した呼び出し部分をまとめて名前をつけ、カスタムのクエリー用メソッドとして使用することができる。繰り返し利用される絞り込み条件をスッキリ読みやすくできる。
scopeを定義するにはモデルに記述する。例えばrecentというscopeを定義したい場合は一つの例としてこういうものがある。
scope :recent, -> { order(created_at: :desc) }

・フィルタを使用し重複を避けることでDRYなコードができる。

・data型は年月日を扱う型となる。時間や秒まで扱いたいときはdatatimeを扱う。

・Railsでのデータ作成や更新は、必ずバリデーションが行われるわけではない。直接SQLを実行するメソッドがある。

・before_saveやbefore_destroyのタイミングで、保存処理や削除処理をキャンセルすることができる。
before_saveやbefore_destroyなどのコールバックにて、throw(:abort)を記載することでデータを保存させないことができます。

・基本的に下記の様にwhereの中身に対して、そのままparamsを設定するのはよくありません。
users = User.where("name LIKE #{params[:name]}")
理由としてはparams[:name]にはどの様な値がくるか分からず、params[:name]の内容に特定のSQLを実行させる様な値がくるかもしれません。
例えば、params[:name]に'a' or 1 = 1という値が設定された場合は下記の様なSQLが実行され、usersテーブルの全てのデータが返されることになります。
SELECT `users`.* FROM `users` WHERE (name LIKE 'a' OR 1 = 1)
上記の例ではそこまで問題が無いかもしれませんが、場合によっては表示したく無いデータが表示されてしまったりすることがあります。
対策として、下記の様に?を使ってパラメータを渡します。
users = User.where('name LIKE ?', params[:name])

・下記の様に引数を渡すことができます。
scope :by_family_name, -> (family_name) { where(family_name: family_name) }
scopeはwhereやorder、他のscopeと組み合わせて使うことができます。

・scopeとクラスメソッドは異なる挙動をします。
scopeはnilを返すことが無く、クラスメソッドはnilを返すことがあります。
そのためメソッドチェインして使う場合にクラスメソッドの場合はnilを返すことがあるため、NoMethodErrorが発生することがありますが、scopeの場合はnilを返さないので、NoMethodErrorが発生することがありません。
