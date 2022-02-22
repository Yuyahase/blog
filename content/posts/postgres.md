

## initdbコマンド
データベースクラスタを作成するコマンド
一つのデータベースクラスタに、複数のデータベースを作成できる

サポート期限はメジャーバージョンの最初のリリースから5年間と定められている

バージョン番号は二つの数字をコンマで区切った`x.y`形式で表される

## 設定ファイル

`postgresql.conf`はostgreSQL全体の動作を制御する設定ファイル。

デフォルトの分離レベルは`read committed`である

以下の分離レベルが設定できる
 - READ UNCOMMITTED
 - READ COMMITTED
 - REPETABLE READ
 - SERIALIZABLE

ログ出力内容は`postgresql.conf`ファイルのパラメータによって設定する