+++
title = "SSL証明書を自作せずに、Railsの開発環境でhttps接続する方法"
date = "2021-12-31"
author = "kseki"
# cover = "img/hello.jpg"
description = "https接続するために自己証明書(オレオレ証明書)を作成するのはまぁ手間がかかります。しかし、localhostというGemを使えばSSL証明書を自作せずにRailsの開発環境でhttps接続できます。"
+++

### 概要

https接続するために自己証明書(オレオレ証明書)を作成するのはまぁ手間がかかります。  
しかし、localhostというGemを使えばSSL証明書を自作せずにRailsの開発環境でhttps接続できます。

### localhost Gemとは

[localhost Gem](https://github.com/socketry/localhost)とは、**Gemを追加するだけ**で`https://localhost:8080`のようにSSL接続できる便利なライブラリです。  
自動的に秘密鍵及び、自己証明書（オレオレ証明書）を作成してくれます。


### 設定方法

開発環境だけlocalhost Gemを使いたいので、GemfileのdevelopmentグループブロックにGemを追加。

```ruby
# Gemfile
group :development do
  gem 'localhost'
end
```

### 実行

Pumaコマンドであれば、Gem追加だけでhttps接続ができます。

```bash
$ bundle exec puma -b 'ssl://localhost:9292' config.ru

Puma starting in single mode...
* Puma version: 5.5.2 (ruby 3.0.2-p107) ("Zawgyi")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 14848
* Listening on ssl://127.0.0.1:9292?
* Listening on ssl://[::1]:9292?
Use Ctrl-C to stop
```
ただ、これだとアプリケーションログは`log/development.log`に出力されるので、別タブを開いて`tail`コマンド等で参照するしかありません。
`rails server`で実行できた方が何かと便利です。

### Rails serverで動かす

Pumaの設定ファイルにSSL接続の設定を追加します。  
Railsデフォルトの設定で3000番ポートは使われているので3001番ポートを指定します。

```ruby
# config/puma.rb
require 'localhost/authority'
authority = Localhost::Authority.fetch

ssl_bind '127.0.0.1', '3001', {
  key: authority.key_path,
  cert: authority.certificate_path
}
```

Railsコマンドで実行する。

```bash
$ bundle exec rails server

=> Booting Puma
=> Rails 7.0.0 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 5.5.2 (ruby 3.0.2-p107) ("Zawgyi")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 17087
* Listening on ssl://127.0.0.1:3001?cert=/Users/kseki/.localhost/localhost.crt&key=/Users/kseki/.localhost/localhost.key&verify_mode=none
* Listening on http://127.0.0.1:3000
* Listening on http://[::1]:3000
Use Ctrl-C to stop
```

リスニングしているポートが２つあります。  
`https://localhost:3001`にアクセスすると続けてアプリケーションログも出力されます。  

```log
Started GET "/" for 127.0.0.1 at 2021-12-31 23:20:45 +0900
Processing by HomeController#index as HTML
  Rendering layout layouts/application.html.erb
  Rendering home/index.html.erb within layouts/application
  Rendered home/index.html.erb within layouts/application (Duration: 0.0ms | Allocations: 15)
  Rendered layout layouts/application.html.erb (Duration: 0.6ms | Allocations: 484)
Completed 200 OK in 1ms (Views: 1.0ms | Allocations: 733)
```
当然`http://localhost:3000`にアクセスしても動きます。

### ポート3000番で接続したい

更にhttpsだけで接続するようにするにはPumaの設定を変更します。

- `ssl_bind`メソッドの引数を`3001`から`3000`に変更
- `PORT`指定行をコメントアウト

```diff
# config/puma.rb

- ssl_bind '127.0.0.1', '3001', {
+ ssl_bind '127.0.0.1', '3000', {

- port ENV.fetch("PORT") { 3000 }
+ # port ENV.fetch("PORT") { 3000 }
```

```bash
$ bundle exec rails server

=> Booting Puma
=> Rails 7.0.0 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 5.5.2 (ruby 3.0.2-p107) ("Zawgyi")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 20097
* Listening on ssl://127.0.0.1:3000?cert=/Users/kseki/.localhost/localhost.crt&key=/Users/kseki/.localhost/localhost.key&verify_mode=none
Use Ctrl-C to stop

```

`https://localhost:3000`にアクセスすると続けてアプリケーションログが出力されます。

```log
Started GET "/" for 127.0.0.1 at 2021-12-31 23:28:51 +0900
Processing by HomeController#index as HTML
  Rendering layout layouts/application.html.erb
  Rendering home/index.html.erb within layouts/application
  Rendered home/index.html.erb within layouts/application (Duration: 1.4ms | Allocations: 448)
  Rendered layout layouts/application.html.erb (Duration: 4.6ms | Allocations: 1473)
Completed 200 OK in 12ms (Views: 8.2ms | Allocations: 4696)
```

### まとめ

[Gist](https://gist.github.com/kseki/bdd31b60f3ce1efff7846849687b26ea)に必要箇所だけまとめたので御覧ください。

### 参考

- [socketry/localhost](https://github.com/socketry/localhost)
- [socketry/localhost: Localhost::Authority](https://github.com/socketry/localhost/blob/v1.1.9/lib/localhost/authority.rb)
- [puma/puma: Self-signed SSL certificates (via the localhost gem, for development use)](https://github.com/puma/puma#self-signed-ssl-certificates-via-the-localhost-gem-for-development-use)
- [Method: Puma::DSL\#ssl\_bind — Documentation for puma \(5\.5\.2\)](https://www.rubydoc.info/gems/puma/Puma%2FDSL:ssl_bind)
