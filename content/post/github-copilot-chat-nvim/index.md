---
title: NeovimでGitHub Copilot Chatを使う方法
description: NvimでGitHub Copilot Chatを使う方法と設定の説明
slug: github-copilot-chat-nvim
date: 2024-04-09T16:30:42+09:00
image: copilot-chat-demo.jpg
categories:
    - エディター
    - AI
tags:
    - Neovim
    - AI
    - AIツール
    - ChatGPT
    - Neovimプラグイン
weight: -10
---

## 概要

GitHub Copilot Chatは、開発者がコーディング中にリアルタイムで支援を受けることができるツールです。
[copilot.vim](https://github.com/github/copilot.vim) を使えばNeovimでもGitHub Copilotは使えるのですが、まだChatには対応していません。
Neovimエディタで利用することで、コードの自動補完やドキュメントの参照、コーディングの疑問点を解決するための対話が可能になります。

[CopilotChat.nvim](https://github.com/CopilotC-Nvim/CopilotChat.nvim)を使うことでChatを利用できるようになります。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/CopilotC-Nvim/CopilotChat.nvim" data-iframely-url="//iframely.net/8ZyPqp6?card=small"></a></div></div>

## 設定方法

### 1. プラグインをインストールする

まずプラグインをインストールする必要があります。お使いのプライグインマネージャーに応じて設定してください。
自分の環境は[vim-plug](https://github.com/junegunn/vim-plug) なので下記を追加しました。

```vim
call plug#begin()
Plug 'zbirenbaum/copilot.lua'
Plug 'nvim-lua/plenary.nvim'
Plug 'CopilotC-Nvim/CopilotChat.nvim', { 'branch': 'canary' }
call plug#end()

lua << EOF
require("CopilotChat").setup {}
EOF
```

その後、`:PackerSync`コマンドを実行しインストール後にNeovimを再起動すると完了です。

### 2. GitHub Copilot Chatが日本語で回答するように設定する

```vim
lua << EOF
require("CopilotChat").setup({
    show_help = "yes",
    prompts = {
        Explain = {
          prompt = "/COPILOT_EXPLAIN アクティブな選択範囲の説明を段落形式で書いてください。日本語で返答ください。",
        },
        Review = {
          prompt = "/COPILOT_REVIEW 選択されたコードをレビューしてください。日本語で返答ください。",
        },
        FixCode = {
          prompt = "/COPILOT_GENERATE このコードには問題があります。バグを修正したコードに書き直してください。日本語で返答ください。",
        },
        Refactor = {
          prompt = "/COPILOT_GENERATE 明瞭性と可読性を向上させるために、次のコードをリファクタリングしてください。日本語で返答ください。",
        },
        BetterNamings = {
          prompt = "/COPILOT_GENERATE 選択されたコードの変数名や関数名を改善してください。日本語で返答ください。",
        },
        Documentation = {
          prompt = "/COPILOT_GENERATE 選択範囲にドキュメントコメントを追加してください。日本語で返答ください。",
        },
        Tests = {
          prompt = "/COPILOT_GENERATE コードのテストを生成してください。日本語で返答ください。",
        },
        Wording = {
          prompt = "/COPILOT_GENERATE 次のテキストの文法と表現を改善してください。日本語で返答ください。",
        },
        Summarize = {
          prompt = "/COPILOT_GENERATE 選択範囲の要約を書いてください。日本語で返答ください。",
        },
        Spelling = {
          prompt = "/COPILOT_GENERATE 次のテキストのスペルミスを修正してください。日本語で返答ください。",
        },
        FixDiagnostic = {
          prompt = "ファイル内の次の問題を支援してください:",
          selection = select.diagnostics,
        },
        Commit = {
          prompt = "変更のコミットメッセージをcommitizenの規約に従って日本語で書いてください。タイトルは最大50文字、メッセージは72文字で折り返してください。メッセージ全体をgitcommit言語のコードブロックで囲んでください。",
          selection = select.gitdiff,
        },
        CommitStaged = {
          prompt = "変更のコミットメッセージをcommitizenの規約に従って日本語で書いてください。タイトルは最大50文字、メッセージは72文字で折り返してください。メッセージ全体をgitcommit言語のコードブロックで囲んでください。",
          selection = function(source)
            local select = require("CopilotChat.select")
            return select.gitdiff(source, true)
          end,
        },
    },
})
EOF
```

`COPILOT_EXPLAIN`などはデフォルトでプロンプトの内容が定数化されています。(詳しくは、[prompts.lua](https://github.com/CopilotC-Nvim/CopilotChat.nvim/blob/canary/lua/CopilotChat/prompts.lua) を参照ください。)
続けて日本語で説明してもらうようにプロンプトを追加しています。

## 使い方

使い方はいろいろありますが、今回は、`:CopilotChatTests`コマンドを使ってテストを作成してもらう方法を書きます。

1. 対象となるコードをヤンクする
2. コマンドモードで`:CopilotChatTests`を入力するとChatが返ってくる
3. チャット画面で`gy`をしてテストコードをヤンクする
4. テストファイルに`p`でペースト

で完成です。

![demo.gif](copilot-chat-demo.gif)

## まとめ

GitHub Copilot Chatは開発者にとって強力な支援ツールです。このツールを活用することで、開発の効率化とコーディングの質の向上が期待できます。

このポストは、NeovimでGitHub Copilot Chatを使う方法についての概要を提供しています。より詳細な情報やサポートが必要な場合は、公式ドキュメントやコミュニティフォーラムを参照してください。開発の旅において、GitHub Copilot Chatが有益なガイドとなることを願っています。

{{< product_card_amazon asin="B0BW4BKR5G" title="ChatGPT超実践活用法: 【GPT-4対応版】【code interpreter解説有】｢ビジネスシーン｣におけるマジで使える利用方法25選【使い方・入門・教科書・初心者・利用法】 ChatGPT・IT・テクノロジー (AI技術・テクノロジー・人工知能)" image="81z9ZVOfGkL" >}}

{{< product_card_amazon asin="4295018236" title="エンジニアのためのChatGPT活用入門 AIで作業負担を減らすためのアイデア集" image="81UJ+mKvUYL" >}}
