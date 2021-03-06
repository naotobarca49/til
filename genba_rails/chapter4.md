# 現場Rails Chapter4-1

・マイグレーション・・・データベース内にテーブルやカラム、インデックス等を追加/削除したり、既存データベースに修正を加えることを楽にできる。以下の2ステップで行うことができる。
1. DBの構造（スキーマ）を変更するコードをRubyで記述した「マイグレーション」ファイルを作成する。
2. 作成した「マイグレーション」ファイルをrails db:migrateコマンドを使ってデータベースに適用する（migrateする。）

・マイグレーションにおいては、１つのマイグレーション（ファイル）が１つのバージョンとして扱われる。つまり、１つのマイグレーションを適用することでデータベースのスキーマのバージョンを１つあげることができ、適用ずみのマイグレーションを１つ取り消すことでバージョンを一つ下げることができる。

・マイグレーションはデータベースごとに適用する必要がある。デフォルトでは開発用データベースに対してマイグレーションが実施される。
bin/rails db:migrateでマイグレーションを実行できる。もし本番用にデータベースにマイグレーションを実施したければmigrateコマンドにRAILS_ENV=productionを付ける。
同様にテスト用データベースにマイグレーションを実施したいときはRAILS_ENV=testをつける。

・Railsアプリのデータベースでは、どこまでマイグレーションが当たっているかが自己管理されている。データベース内に作成される「schema_migrations」というテーブルを通じて行われ、どのマイグレーション（バージョン）が適用ずみかをデータとして保存する。これによってrails migrateコマンドは、飛び石でマイグレーションが適用されているような状況でも、適用していないマイグレーションだけを適用して進んでくれる。

・rails db:migrate　→　バージョンをあげるとき。　rails db:rollback　→　バージョンを下げるとき。

・マイグレーションファイルではchangeというメソッドがあるが、これは一見するとバージョンをあげるときのコードだけが書かれているように見える。これは、機械的に反対の処理を生み出せるようなケースでは、プログラマはバージョンをあげる側のコードだけをchangeメソッドに書けばいいことになっているからである。

・マイグレーションの名前は、アプリケーション内で一意である必要がある。

・Railsは、現在のデータベースの構造をdb/schema.rbというファイルに自動出力する。このファイルは、マイグレーションを適用したり外したりすると自動的に出力される。また、db:schema:dumpコマンドで、手動で出力することもできる。schema.rbは自動テスト環境などで利用されるほか、視覚的に、テーブル・カラム構造を確認できるため、データベースについての手軽なドキュメントとして利用することができる。

・bin/rails db:migrate・・・最新までマイグレーションを適用する
・bin/rails db:migrate VERSION=XXXXXXXXXXX・・・特定バージョンまでマイグレーションが適用された状態にする。VERSIONにはマイグレーションファイル名の先頭の数字部分(日時部分)を指定する。
・bin/rails db:rollback・・・バージョンを一つ戻す。
・bin/rails db:rollback STEP=2・・・バージョンを指定したステップ数だけ戻す
・bin/rails db:migtate:redo・・・バージョンを一つ戻してから一つあげる・（バージョンは最終的に変化しないが、バージョンを戻す処理が想定通り動作することを簡単に確認できる。）

・マイグレーション中にエラーが起きた場合は、問題のおきたマイグレーションで適用しかけた変更はロールバックされ、それ以降のマイグレーションも適用されない。つまり、問題のおきたマイグレーションを適用する前の状態のままとなる。コードを修正して、また適用を試みよう。（MySQL5.7など、一部のデータベースではロールバックしてくれず、中途半端な状態になることがある。その場合は、コードを逐次コメントアウトしてバージョンの上げ下げを行ったり、直接データベースの状態を変更するなどの調整が必要。）
