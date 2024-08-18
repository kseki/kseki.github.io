---
title: NeovimでALEを使ってDocker Compose上でRubocopを実行する
description: Neovim(Vim) + ALEを使ってDocker Compose上でRubocopを実行する方法
slug: ale-vim-rubocop-on-docker-compose
date: 2022-01-20
image: vim-ale-rubocop.png
categories:
    - エディター
tags:
    - Vim
    - Neovim
    - Vimプラグイン
    - Docker Compose
    - Linter
weight: 1
---

## 概要

昨今の開発環境はほとんどのロジェクトでDocker Composeなどの仮想環境で構成されています。  
今回は、Neovim(Vim) + ALEを使ってDocker Compose上でRubocopを実行する方法を書きました。

### バージョン

- Neovim: v0.6.0
- ALE: v3.1.0
- Docker Compose: 1.24.0

## 設定方法

前提として、ALEプラグインをインストール済みの状態であることとします。

まず、Railsのbinstubとして`bin/rubocop`にDocker Compose上でRubocopを実行するスクリプトを作成します。

```ruby
#!/usr/bin/env ruby

require 'pathname'
require 'fileutils'

include FileUtils

APP_ROOT = Pathname.new File.expand_path('../../', __FILE__)

chdir APP_ROOT do
  rubocop_command = "bundle exec rubocop #{ARGV.join(' ')}"
  system("docker-compose exec -T app #{rubocop_command}'")
end
```
ALEはRubocopの結果をJSON形式で受け取って処理します。その際、標準出力に余計な出力がないよう、`docker-compose exec`コマンドに`-T`オプションを指定しています。

#### Neovimの設定

最後にNeovimの設定をします。  
開発環境はプロジェクトによって異なるので、僕は[thinca/vim\-localrc](https://github.com/thinca/vim-localrc)を使って`.local.vimrc`に設定をしています。

#### 1. 実行コマンドに`bin/rubocop`を指定
```vim
let g:ale_ruby_rubocop_executable = 'bin/rubocop'
```


こちらは説明不要ですね。

####  2. ファイル名のマッピング
```vim
let g:ale_filename_mappings = {
  \ 'rubocop': [
  \   ['/Users/kseki/rails-project', '/opt'],
  \ ],
  \}
```

そのままだとローカルの絶対パスを`bin/rubocop`コマンドの引数として渡してしまうが、実行するのはDocker上です。
Docker上の絶対パスに変換してから渡すようにマッピング指定をします。
`['/Users/kseki/rails-project', '/opt']` の部分です。左はローカルのプロジェクトルートパス、右はDocker上のプロジェクトルートパスです。

## まとめ

Gistにまとめました！
[Setting up "Rubocop" to run on "Docker compose" using "ALE"](https://gist.github.com/kseki/811a9c4bd9f7a1c6bcec00691007bcc9)

## 参考

- [dense\-analysis/ale: Check syntax in Vim asynchronously and fix files, with Language Server Protocol \(LSP\) support](https://github.com/dense-analysis/ale)
- [Vim, ALE, Docker, and Per\-Project Linting \| Ryan McGrath](https://rymc.io/blog/2019/vim-ale-docker-per-project-linting/)
