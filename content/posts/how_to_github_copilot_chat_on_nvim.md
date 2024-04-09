+++
title = "NeovimでGitHub Copilot Chatを使う方法"
date = "2024-04-09T16:30:42+09:00"
author = "kseki"
cover = "/img/copilot-chat-demo.jpg"
tags = ["Neovim", "AI"]
categories = ["開発", "効率化"]
description = ""
+++

### 概要

GitHub Copilot Chatは、開発者がコーディング中にリアルタイムで支援を受けることができるツールです。
[copilot.vim](https://github.com/github/copilot.vim) を使えばNeovimでもGitHub Copilotは使えるのですが、まだChatには対応していません。
Neovimエディタで利用することで、コードの自動補完やドキュメントの参照、コーディングの疑問点を解決するための対話が可能になります。

[CopilotChat.nvim](https://github.com/CopilotC-Nvim/CopilotChat.nvim)を使うことでChatを利用できるようになります。

### 設定方法

#### 1. プラグインをインストールする

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

#### 2. GitHub Copilot Chatが日本語で回答するように設定する

```vim
lua << EOF
require("CopilotChat").setup({
    show_help = "yes",
    prompts = {
        Explain = {
  	    	prompt = "/COPILOT_EXPLAIN 上記のコードを日本語で説明してください",
  	    },
  	    Tests = {
  	    	prompt = "/COPILOT_TESTS 上記のコードの詳細なユニットテストを書いてください。説明は日本語でお願いします。",
  	    },
  	    Fix = {
  	    	prompt = "/COPILOT_FIX このコードには問題があります。バグを修正したコードを表示してください。説明は日本語でお願いします。",
  	    },
  	    Optimize = {
  	    	prompt = "/COPILOT_REFACTOR 選択したコードを最適化し、パフォーマンスと可読性を向上させてください。説明は日本語でお願いします。",
  	    },
    },
})
EOF
```

`COPILOT_EXPLAIN`などはデフォルトでプロンプトの内容が定数化されています。(詳しくは、[prompts.lua](https://github.com/CopilotC-Nvim/CopilotChat.nvim/blob/canary/lua/CopilotChat/prompts.lua) を参照ください。)
続けて日本語で説明してもらうようにプロンプトを追加しています。

### 使い方

使い方はいろいろありますが、今回は、`:CopilotChatTests`コマンドを使ってテストを作成してもらう方法を書きます。

1. 対象となるコードをヤンクする
2. コマンドモードで`:CopilotChatTests`を入力するとChatが返ってくる
3. チャット画面で`gy`をしてテストコードをヤンクする
4. テストファイルに`p`でペースト

で完成です。

![demo.gif](/img/copilot-chat-demo.gif)

### まとめ

GitHub Copilot Chatは開発者にとって強力な支援ツールです。このツールを活用することで、開発の効率化とコーディングの質の向上が期待できます。

このブログポストは、NeovimでGitHub Copilot Chatを使う方法についての概要を提供しています。より詳細な情報やサポートが必要な場合は、公式ドキュメントやコミュニティフォーラムを参照してください。開発の旅において、GitHub Copilot Chatが有益なガイドとなることを願っています。
