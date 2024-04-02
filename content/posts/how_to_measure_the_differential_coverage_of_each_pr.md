+++
title = "PR毎にDiffカバレッジを計測する方法"
date = "2024-04-02T16:22:47+09:00"
author = "kseki"
cover = "img/undercover.png"
tags = ["Ruby", "Ruby on Rails", "テスト", "undercover"]
categories = ["開発"]
description = "undercoverというGemを使ってPR毎にDiffカバレッジを計測する方法をまとめました。"
+++

### 概要

プログラミングにおいて、テストカバレッジはコードの品質を保証する重要な指標の1つです。特に、大規模なプロジェクトや多くの開発者が関わるプロジェクトでは、変更が頻繁に行われるため、新たなコードが既存のテストによって適切にカバーされているかを確認することが不可欠です。

このブログポストでは、[undercover](https://github.com/grodowski/undercover) gemを使用して、プルリクエスト（PR）毎にDiffカバレッジを計測し、継続的インテグレーション（CI）で監視する方法について解説します。

### 背景

プロジェクトにアサインされた際、テストカバレッジの低さがバグの多さに直結していることに気づくことは珍しくありません。テストカバレッジを上げることは、バグを減らし、ソフトウェアの品質を向上させるために重要です。しかし、Simplecovだけでは、全体のテストカバレッジしか測定できず、変更点に対するカバレッジを把握できません。これでは、新たなコードが適切にテストされているかを確認することが困難です。

ここで役立つのが、undercover gemです。このgemを使用すると、PR毎に変更されたコードに対するテストカバレッジ、すなわちDiffカバレッジを計測できます。これにより、CIツール内での監視が可能になり、新しいコードが十分にテストされていることを保証できます。

Diffカバレッジを計測することの利点は、開発者が新しいコードに対するテストを怠らないように促すことです。また、レビュアーがコードレビューする際に、テストが適切に行われているかを確認するための指標としても機能します。さらに、テストカバレッジを徐々に上げることで、プロジェクト全体の品質を向上させることができます。

### 設定方法

undercover gemを使ったDiffカバレッジの計測方法は以下の通りです。

Gemfileにundercoverを追加し、`bundle install`を実行します。
undercoverが対応しているのはLCOV形式なので、simplecov-lcovも一緒にインストールします。

```ruby
# Gemfile
group :test do
  gem 'simplecov', require: false
  gem 'simplecov-lcov', require: false
  gem 'undercover', require: false
end
```

次にsimplecovの設定を追加します。

```ruby
# spec/rails_helper.rb
require 'simplecov'
require 'simplecov-lcov'

SimpleCov::Formatter::LcovFormatter.config.report_with_single_file = true
SimpleCov.formatter = SimpleCov::Formatter::LcovFormatter
SimpleCov.start

require 'undercover'
```

最後に、CIツール(今回はGitHub Actions)の設定ファイルに、PRが作成された際にundercoverを実行するステップを追加します。
基本的にはフューチャーブランチでPRを作成するので、mainとのDiffカバレッジを取得するように設定します。

```yaml
# .github/workflows/test.yml

steps:
  ~ 省略 ~

  - name: Test
    run: bundle exec rspec

  - name: Diff Coverage
    run: |
      git fetch origin main
      undercover --compare origin/main
```

PRがマージされる前に、undercoverが生成するレポートを確認し、Diffカバレッジが十分であることを確認します。

![alt text](/img/undercover.png)

### まとめ

undercover gemを導入することで、PR毎に新たに追加または変更されたコードがどの程度テストによってカバーされているかを正確に把握できるようになりました。これにより、テスト漏れが発生するリスクを減らし、コードの品質を継続的に向上させることができます。また、開発者は自分のコードが十分にテストされているかを自己確認できるため、品質意識の向上にも寄与します。

### 参考

- [Support for "diff coverage" · Issue #886 · simplecov-ruby/simplecov](https://github.com/simplecov-ruby/simplecov/issues/886)
- [grodowski/undercover](https://github.com/grodowski/undercover)
