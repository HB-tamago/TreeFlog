# TreeFlog-

* C++によるフルスタックの高速Webアプリケーションフレームワーク
* C++/Qt で作られたサーバサイドのフレームワークである
* MVC アーキテクチャ,O/R マッパー,テンプレートの仕組みも整備
* テンプレートは独自のOtamaかerbファイルをしようできる
* BSDライセンス
* 開発が日本人なので日本語のドキュメントのほうが充実している

## 概要
## インストール

### 必要なライブラリをインストール

```
sudo apt-get install -y qt5-default qt5-qmake libqt5sql5-mysql libqt5sql5-psql libqt5sql5-odbc libqt5sql5-sqlite libqt5core5a libqt5qml5 libqt5xml5 qtbase5-dev qtdeclarative5-dev qtbase5-dev-tools gcc g++ make
sudo apt-get install -y libmysqlclient-dev libpq5 libodbc1
```

* ファイルをダウンロードする

```
# wget http://downloads.sourceforge.net/project/treefrog/src/treefrog-1.12.0.tar.gz
```

* 展開

```
# tar zxf treefrog-1.12.0.tar.gz
```

* インストール

```
root@debian-8:~# cd treefrog-1.12.0/
root@debian-8:~/treefrog-1.12.0# ./configure
Reading /root/treefrog-1.12.0/tools/tfmanager/tfmanager.pro
Reading /root/treefrog-1.12.0/tools/tfserver/tfserver.pro
Reading /root/treefrog-1.12.0/tools/tmake/tmake.pro
Reading /root/treefrog-1.12.0/tools/tspawn/tspawn.pro

Compiling MongoDB driver library ... OK

First, run "make" and "sudo make install" in src directory.
Next, run "make" and "sudo make install" in tools directory.
```

```
root@debian-8:~/treefrog-1.12.0# cd src/
root@debian-8:~/treefrog-1.12.0/src# make
root@debian-8:~/treefrog-1.12.0/src# make install
```

```
root@debian-8:~/treefrog-1.12.0/src# cd ../tools/
root@debian-8:~/treefrog-1.12.0/tools# make
root@debian-8:~/treefrog-1.12.0/tools# make install
```

* 共有ライブラリの依存関係情報を更新

```
ldconfig
```

## アプリケーション作成

### アプリケーションのスケルトンを作成

tspawnコマンドでスケルトンを作成する

```
root@debian-8:/home/isucon/private_isu/webapp# tspawn new c++
  created   c++
  created   c++/controllers
  created   c++/models
  created   c++/models/sqlobjects
  created   c++/models/mongoobjects
  created   c++/views
  created   c++/views/layouts
  created   c++/views/mailer
  created   c++/views/partial
  created   c++/views/_src
  created   c++/helpers
  created   c++/config
  created   c++/config/environments
  created   c++/config/initializers
  created   c++/public
  created   c++/public/images
  created   c++/public/js
  created   c++/public/css
  created   c++/db
  created   c++/lib
  created   c++/log
  created   c++/plugin
  created   c++/script
  created   c++/sql
  created   c++/test
  created   c++/tmp
  created   c++/c++.pro
  created   c++/appbase.pri
  created   c++/controllers/controllers.pro
  created   c++/controllers/applicationcontroller.h
  created   c++/controllers/applicationcontroller.cpp
  created   c++/models/models.pro
  created   c++/views/views.pro
  created   c++/views/_src/_src.pro
  created   c++/views/layouts/.trim_mode
  created   c++/views/mailer/.trim_mode
  created   c++/views/partial/.trim_mode
  created   c++/helpers/helpers.pro
  created   c++/helpers/applicationhelper.h
  created   c++/helpers/applicationhelper.cpp
  created   c++/config/application.ini
  created   c++/config/database.ini
  created   c++/config/development.ini
  created   c++/config/logger.ini
  created   c++/config/mongodb.ini
  created   c++/config/redis.ini
  created   c++/config/routes.cfg
  created   c++/config/validation.ini
  created   c++/config/initializers/internet_media_types.ini
  created   c++/public/403.html
  created   c++/public/404.html
  created   c++/public/413.html
  created   c++/public/500.html
  created   c++/script/JSXTransformer.js
  created   c++/script/react.js
  created   c++/script/react-with-addons.js
  created   c++/script/react-dom-server.js
```

## DBを作成

ISUCONでは、既に作成されているデータを読み込むのでskipp
使用するデータベースは isuconp

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| isuconp            |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.05 sec)
```

* テーブル

```
mysql> show tables;
+-------------------+
| Tables_in_isuconp |
+-------------------+
| comments          |
| posts             |
| users             |
+-------------------+
3 rows in set (0.00 sec)
```

## データベースの情報を設定

データベースの情報を config/database.ini ファイルに設定します。
エディタでファイルを開き、[dev] の各項目に環境に応じた適切な値を入力して、保存します。

```
root@debian-8:/home/isucon/private_isu/webapp/c++/config# cp -p database.ini{,.ord}
root@debian-8:/home/isucon/private_isu/webapp/c++/config# vim database.ini
root@debian-8:/home/isucon/private_isu/webapp/c++/config# diff -u database.ini{,.ord}
--- database.ini	2016-08-07 02:16:35.363148865 +0900
+++ database.ini.ord	2016-08-07 02:10:23.065091461 +0900
@@ -17,11 +17,11 @@
 # DatabaseName=db/dbfile

 [dev]
-DriverType=QMYSQL
-DatabaseName=isuconp
-HostName=localhost
-Port=3306
-UserName=root
+DriverType=QSQLITE
+DatabaseName=db/dbfile
+HostName=
+Port=
+UserName=
 Password=
 ConnectOptions=
 ```

 正しく設定されたか、DBにアクセスしてテーブルを表示してみましょう。

 ```
 root@debian-8:/home/isucon/private_isu/webapp/c++# tspawn --show-tables
DriverType:   QMYSQL
DatabaseName: isuconp
HostName:     localhost
Database opened successfully
-----------------
Available tables:
  comments
  posts
  users
```

正しくDBにアクセスできることを確認


## テンプレートシステムの指定

ERBまたはOtamaのどちらかを選択する。今回はrailsのerbファイルをそのまま使うために
ERBにする

development.ini ファイルにある TemplateSystem パラメータに設定します。

config/development.iniファイルを編集する必要があるが今回は必要なし

## 作ったテーブルからコードを自動生成

コマンドラインから、ジェネレータコマンド（tspawn）を実行し、ベースとなるコードを生成します。
下記の例ではコントローラ、モデル、ビューを生成しています。引数には、テーブル名を指定します。

```
root@debian-8:/home/isucon/private_isu/webapp/c++# tspawn scaffold comments
DriverType:   QMYSQL
DatabaseName: isuconp
HostName:     localhost
Database opened successfully
  created   models/sqlobjects/commentsobject.h
  created   models/comments.h
  created   models/comments.cpp
  updated   models/models.pro
  created   controllers/commentscontroller.h
  created   controllers/commentscontroller.cpp
  updated   controllers/controllers.pro
  created   views/comments
  created   views/comments/.trim_mode
  created   views/comments/index.erb
  created   views/comments/show.erb
  created   views/comments/entry.erb
  created   views/comments/edit.erb

 Index page URL:  http://localhost:8800/Comments/index

Run `qmake -r CONFIG+=debug` to generate a Makefile for debug mode.
Run `qmake -r CONFIG+=release` to generate a Makefile for release mode.
root@debian-8:/home/isucon/private_isu/webapp/c++# tspawn scaffold posts
DriverType:   QMYSQL
DatabaseName: isuconp
HostName:     localhost
Database opened successfully
  created   models/sqlobjects/postsobject.h
  created   models/posts.h
  created   models/posts.cpp
  updated   models/models.pro
  created   controllers/postscontroller.h
  created   controllers/postscontroller.cpp
  updated   controllers/controllers.pro
  created   views/posts
  created   views/posts/.trim_mode
  created   views/posts/index.erb
  created   views/posts/show.erb
  created   views/posts/entry.erb
  created   views/posts/edit.erb

 Index page URL:  http://localhost:8800/Posts/index

root@debian-8:/home/isucon/private_isu/webapp/c++# tspawn scaffold users
DriverType:   QMYSQL
DatabaseName: isuconp
HostName:     localhost
Database opened successfully
  created   models/sqlobjects/usersobject.h
  created   models/users.h
  created   models/users.cpp
  updated   models/models.pro
  created   controllers/userscontroller.h
  created   controllers/userscontroller.cpp
  updated   controllers/controllers.pro
  created   views/users
  created   views/users/.trim_mode
  created   views/users/index.erb
  created   views/users/show.erb
  created   views/users/entry.erb
  created   views/users/edit.erb

 Index page URL:  http://localhost:8800/Users/index
```

※ tspawn の オプションによって、コントローラだけ、あるいはモデルだけ生成するように変えられるので
　作成はモデルとコントローラだけでいいかも

## ソースコードをビルド

下記コマンドでソースMakefileを作成

```
qmake -r "CONFIG+=debug"
root@debian-8:/home/isucon/private_isu/webapp/c++# qmake -r "CONFIG+=debug"
Reading /home/isucon/private_isu/webapp/c++/helpers/helpers.pro
Reading /home/isucon/private_isu/webapp/c++/models/models.pro
Reading /home/isucon/private_isu/webapp/c++/views/views.pro
 Reading /home/isucon/private_isu/webapp/c++/views/_src/_src.pro
WARNING: Failure to find: comments_editView.moc
WARNING: Failure to find: comments_entryView.moc
WARNING: Failure to find: comments_indexView.moc
WARNING: Failure to find: comments_showView.moc
Reading /home/isucon/private_isu/webapp/c++/controllers/controllers.pro
```

WARNINGメッセージが表示されるが問題はない
Makefileが作成されたらmakeコマンドを実行

```
root@debian-8:/home/isucon/private_isu/webapp/c++# ls
appbase.pri  config  controllers  c++.pro  db  helpers	lib  log  Makefile  models  plugin  public  script  sql  test  tmp  views
root@debian-8:/home/isucon/private_isu/webapp/c++# make
```

## アプリケーションサーバ起動

```
treefrog -e dev
```

この状態でアクセスするとテーブルの全一覧を表示させることができる。

# sinatra から C++への実装

上記手順でコントローラなどができているので後はそこを変更していくだけ
今回はsinatraからC++への実装

```
def get_session_user()
  if session[:user]
    db.prepare('SELECT * FROM `users` WHERE `id` = ?').execute(
      session[:user][:id]
    ).first
  else
    nil
  end
end

get '/' do
  me = get_session_user()

  results = db.query('SELECT `id`, `user_id`, `body`, `created_at`, `mime` FROM `posts` ORDER BY `created_at` DESC')
  posts = make_posts(results)

  erb :index, layout: :layout, locals: { posts: posts, me: me }
end
```

上記は

get メソッドで "/" にアクセスした時

routingはconfig/routes.cfgに記述する。

下記が今回あるrouting
(処理は最初考えない)

```
root@debian-8:/home/isucon/private_isu/webapp/ruby# grep "post '" app.rb
    post '/login' do
    post '/register' do
    post '/' do
    post '/comment' do
    post '/admin/banned' do
root@debian-8:/home/isucon/private_isu/webapp/ruby# grep "get '" app.rb
    get '/initialize' do
    get '/login' do
    get '/register' do
    get '/logout' do
    get '/' do
    get '/@:account_name' do
    get '/posts' do
    get '/posts/:id' do
    get '/image/:id.:ext' do
    get '/admin/banned' do
```

上記の通りroutes.cfgに記述

```
get /initialize
get /login
get /register
get /
get /:account_name
get /posts
get /posts/:id
get /image/:id.:ext
get /admin/banned
post /login
post /register
post /
post /comment
post /admin/banned
```

## SQLの書き方

TSqlQueryクラスを使用

```
TSqlQuery query;
query.prepare("SELECT * FROM users WHERE id = ?")
query.exec(post.id);
```

上記のように使用

rubyのviews配下のerbファイルを
