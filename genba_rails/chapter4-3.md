# 現場Rails Chapter4-3  

・Webアプリケーションの使い勝手としては、データベースの定義で簡単に実現できる部分だけでは物足りない。まず、制限の種類として足りない場合がある。データベースで定義することが不可能では無いものの、チェックコードの開発のしやすさやデータベースの扱いやすさを重視して、一般的にはモデルでそのような制限を守っているかのチェックを行う。またデータベースでの制限は、違反があったときに、丁寧なわかりやすいエラーメッセージをユーザーに届けるということに関しても物足りない。

・データの内容が正しいかどうかをチェックする仕組みのことをバリデーション（検証）と呼ぶ。モデルの検証を使用することで、非常に自由度の高いチェックを行った上で、ユーザーにエラーをわかりやすく伝えることができる。

・モデルの検証さえしっかりやれば、データベースの定義でデータの範囲をあまり制限しなくても、データの状態はそこそこ正しい状態を保つことができる。とはいえ、データベースを直接触ったりすることもあるため、データベーす側で手軽にできるデータの制限も確実に行うこともした方が良い。一意性の検証のように、モデルの通常の検証ではデータ不正を完全には防止できない性質のものもある。

・モデルの検証は基本的に「モデルオブジェクト（レコード）をデータベースに登録・更新する前に検証を行い、エラーがあれば登録・更新をしないで差し戻す」という仕組みになっている。この仕組みの登録・更新に対応するメソッドがsaveである。

・saveメソッドは、データベースの登録・更新を行う前に自動的に検証を行い、検証エラーがあればfalseを返す。検証エラーの詳細には、errorsを通じてアクセスできる。検証エラーがなければ、データベースへの登録・更新を行なってtrueを返す。この時、検証失敗以外の予期せぬエラーがあれば、例外を発生させる。
save!メソッドは検証エラー時にfalseを返すのではなく例外を発生させるメソッドである。save!を使った場合は、例外が出なければデータベースへの登録・更新が間違いなく行われたと考えることができる。基本的に登録・更新が成功するはずと信じている場面では、saveよりもsave!を使用することで予期せぬ失敗を防ぐことができる。
saveとsave!を使い分けることで戻り値を制御できるということ。

・検証は、valid?メソッドを使用することで検証単独処理を呼ぶこともできる。

・task.save(validates: false)と叩くことで検証を意図的に回避することができる。通常あまり使用しないが、これは検証エラーになることがわかっているが特別にデータを登録・更新したいときに使用する。

・検証の書き方は２つある
1. Railsが用意している検証用のヘルパーを利用する
2. 自分で任意の検証コードを記述する

・persisted?メソッドはそのオブジェクトがデータベースに保存済みかどうかを確認できる。

・createアクションでエラーメッセージを表示する時にはインスタンス変数にオブジェクトを格納すること
エラーメッセージを表示することができるし、エラーが起きた時にもう一度新規登録画面を表示する際に、viewに検証を行なった現物のオブジェクトを渡す必要がある。

・ぼっち演算子はレシーバがnilでもメソッドが使用できる。レシーバがnilの場合はnilを返す。
