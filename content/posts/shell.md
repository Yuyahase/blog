### envコマンド
環境変数を一時的に変更したり、削除するコマンド

|オプション|説明|
|-|-|
|-i|何も環境変数が設定されていない状態にする|
|-u|環境変数を一時的に削除|
|オプションなし|全ての環境変数を表示|


### setコマンド

|オプション|説明|
|-|-|
|allexport|新規作成・変更した変数を自動的に環境変数とする|
|emacs|emacsエディタと同じキービンどにする|
|ignoreeof|Ctrl + Dを押してもログアウトしない設定にする|
|noclobber|リダイレクト演算子で既存ファイルの上書きを無効にする|
|noglob|パス名展開を向こうにする(*や?を無効にする)|
|noexec|シェルスクリプトを読み込むが、構文エラーのみ確認する|

bash起動時に実行される主な環境設定ファイル

/etc/profile
/.bash_profile
/.bash_login
/.profile