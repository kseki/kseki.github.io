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

<div class="iframely-embed"><div class="iframely-responsive" style="padding-bottom: 52.5%; padding-top: 120px;"><a href="https://www.amazon.co.jp/ChatGPT%E8%B6%85%E5%AE%9F%E8%B7%B5%E6%B4%BB%E7%94%A8%E6%B3%95-interpreter%E8%A7%A3%E8%AA%AC%E6%9C%89%E3%80%91%EF%BD%A2%E3%83%93%E3%82%B8%E3%83%8D%E3%82%B9%E3%82%B7%E3%83%BC%E3%83%B3%EF%BD%A3%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B%E3%83%9E%E3%82%B8%E3%81%A7%E4%BD%BF%E3%81%88%E3%82%8B%E5%88%A9%E7%94%A8%E6%96%B9%E6%B3%9525%E9%81%B8%E3%80%90%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%BB%E5%85%A5%E9%96%80%E3%83%BB%E6%95%99%E7%A7%91%E6%9B%B8%E3%83%BB%E5%88%9D%E5%BF%83%E8%80%85%E3%83%BB%E5%88%A9%E7%94%A8%E6%B3%95%E3%80%91-ChatGPT%E3%83%BB-%E3%83%BB%E3%83%86%E3%82%AF%E3%83%8E%E3%83%AD%E3%82%B8%E3%83%BC-AI%E6%8A%80%E8%A1%93%E3%83%BB%E3%83%86%E3%82%AF%E3%83%8E%E3%83%AD%E3%82%B8%E3%83%BC%E3%83%BB%E4%BA%BA%E5%B7%A5%E7%9F%A5%E8%83%BD-ebook/dp/B0BW4BKR5G" data-iframely-url="//iframely.net/cASUCnA"></a></div></div>

<div class="iframely-embed"><div class="iframely-responsive" style="padding-bottom: 52.5%; padding-top: 120px;"><a href="https://www.amazon.co.jp/%E3%82%A8%E3%83%B3%E3%82%B8%E3%83%8B%E3%82%A2%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AEChatGPT%E6%B4%BB%E7%94%A8%E5%85%A5%E9%96%80-AI%E3%81%A7%E4%BD%9C%E6%A5%AD%E8%B2%A0%E6%8B%85%E3%82%92%E6%B8%9B%E3%82%89%E3%81%99%E3%81%9F%E3%82%81%E3%81%AE%E3%82%A2%E3%82%A4%E3%83%87%E3%82%A2%E9%9B%86-%E5%A4%A7%E6%BE%A4-%E6%96%87%E5%AD%9D/dp/4295018236" data-iframely-url="//iframely.net/YBQ3l6P"></a></div></div><script async src="//iframely.net/embed.js"></script>
