---
title: GitHub Copilot Chatのプロンプトをスニペット化し、nvim-cmpと連携する方法
description: GitHub Copilot Chatのプロンプトをスニペット化し、nvim-cmpと連携する具体的な方法を紹介します。Neovimでの開発効率を向上させる設定手順を解説します。
keywords:
    - GitHub Copilot Chat
    - Neovim
    - nvim-cmp
    - スニペット
    - プログラミング
    - 開発ツール
    - プロンプト
    - ChatGPT
    - プロンプトエンジニアリング
slug: github-copilot-chat-snippet
date: 2024-08-20T16:11:47+09:00
image: cover.webp
categories:
    - 開発
    - エディター
tags: 
    - GitHub Copilot
    - Neovim
    - Neovimプラグイン
    - スニペット
    - ChatGPT
    - プロンプトエンジニアリング
weight: 1
draft: false
---

## 概要

前回の [NeovimでGitHub Copilot Chatを使う方法](../github-copilot-chat-nvim) では、GitHub Copilot ChatをNeovimで使う方法を紹介しました。今回は、プロンプトをスニペット化し、nvim-cmpと連携して使う方法を紹介します。

設計や実装の相談など、フォーマットは決まっているが中身が毎回変わるケースや、あらかじめプロンプトのフォーマットが決まっている場面ではスニペット化しておくと便利です。これにより、プロンプトエンジニアリングの効率が大幅に向上します。

## 設定方法

以下の環境を前提に話を進めます。別の環境の場合は適宜読み替えてください。

        $ sw_vers
        ProductName:            macOS
        ProductVersion:         14.6.1
        BuildVersion:           23G93
        
        $ nvim -v
        NVIM v0.10.1
        Build type: Release
        LuaJIT 2.1.1723675123
        
        nvim-cmp
            branch main
            commit ae644fe
        
        CopilotChat.nvim
            branch canary
            commit 1a92bb6
        
        nvim-snippets
            branch main
            commit 56b4052

### nvim-cmpの設定

nvim-cmpの設定を追加します。Copilot Chatのプロンプトがファイルタイプ `copilot-chat` なので、このファイルタイプに対して補完用のソースを追加します。

```lua
local cmp = require("cmp")

cmp.setup.filetype("copilot-chat", {
  sources = {
    { name = "snippets" },
    { name = "copilot" },
    { name = "buffer" },
  },
})
```

### nvim-snippetsの設定

以下の設定を追加します。

※自分は [nvim-snippets](https://github.com/garymjr/nvim-snippets) を使っていますが、他のスニペットプラグインを使っている場合はそのプラグインに合わせて設定してください。

```lua
return {
  "garymjr/nvim-snippets",
  opts = function(_, opts)
    opts.search_path = {
      "~/.config/nvim/snippets",
    }
  end,
  keys = {
    {
      "<Leader>cp",
      function()
        local filetype = vim.bo.filetype
        local path = string.format("~/.config/nvim/snippets/%s.json", filetype)
        vim.cmd("e " .. path)
      end,
      desc = "Open snippets file",
    },
  },
}
```

次に、`~/.config/nvim/snippets/copilot-chat.json` にスニペットを追加します。

```json
{
  "仕様相談": {
    "prefix": "specon",
    "body": [
      "以下の仕様について相談します。注意点・確認点があれば教えてください。",
      "## 技術制約",
      "- ${1:Ruby on Rails7}",
      "## 仕様",
      "- ${2:招待制ユーザー登録機能}"
    ]
  },
  "ライブラリ選定": {
    "prefix": "sllib",
    "body": [
      "以下の仕様を実現するためのライブラリの候補をあげてください。",
      "それぞれ、公式URL・メリット・デメリットを教えてください。",
      "## 技術制約",
      "- ${1:NPM}",
      "## 仕様",
      "- ${2:フォーマッター}"
    ]
  }
}
```

以上で準備は完了です！

## 使い方

動画で使い方を説明します。

![GitHub Copilot Chatのプロンプトをスニペット化して、nvim-cmpを連携して便利に使う方法](try-snippet.gif)

## まとめ

GitHub Copilot Chatとnvim-cmpを連携することで、プロンプト上でスニペットを活用できるようになります。これにより、設計や実装の相談が効率的に行えるようになります。プロンプトエンジニアリングの観点からも、非常に有用な手法です。ぜひ試してみてください。
