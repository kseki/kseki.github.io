---
title: NeovimでTabnineを使う方法
description: NeovimにてTabnineを使って補完をする方法の説明
slug: cmp-tabnine-nvim
date: 2024-07-02T16:38:00+09:00
categories:
    - エディター
    - AI
tags:
    - Neovim
    - Neovimプラグイン
    - AI
    - AIツール
    - ChatGPT
    - Tabnine
weight: 1
---

## Tabnineとは

Tabnineは、AIを活用したコード補完プラグインです。従来の補完機能よりも高度な機能を提供し、コード開発を効率化します。Tabnineは、GPT-3と呼ばれる強力なAIモデルを使用しています。このモデルは、大量のコードを学習し、その結果を元にコード補完を提供します。

主な機能は以下の通りです。

- **コンテキストに応じた補完:** Tabnineは、あなたが書いているコードの文脈を理解し、最も適切な補完候補を提案します。これは、あなたが何をしようとしているかをAIが理解し、それに基づいて最適なコードスニペットを提供することを意味します。

- **コード生成:** Tabnineは、関数やクラスの定義、テストコードなど、様々なコードを自動生成できます。これは、あなたが新しい関数を作成しようとしているとき、Tabnineがその関数のスケルトンを生成することを意味します。

- **コード検索:** Tabnineは、コード内の変数や関数の定義箇所を素早く検索できます。これは、あなたが特定の関数の定義を探しているとき、Tabnineがその関数の定義を素早く見つけることを意味します。

- **マルチ言語対応:** Tabnineは多くのプログラミング言語に対応しており、様々な言語間で補完やコード生成を行うことができます。これは、あなたがPythonでコードを書いているときでも、Javaでコードを書いているときでも、Tabnineが適切な補完を提供することを意味します。

## Neovimにインストールする方法

今回はhrsh7th/nvim-cmpのソースとしてにTabnineをインストールします。

プラグインを追加

```lua
return require("packer").startup(
    use({
        'tzachar/cmp-tabnine',
        run='./install.sh',
        requires = 'hrsh7th/nvim-cmp',
    })
)
```

設定を追加

```lua
require('cmp_tabnine.config').setup({
	max_lines = 1000,
	max_num_results = 20,
	sort = true,
	run_on_every_keystroke = true,
	snippet_placeholder = '..',
	ignored_file_types = {
		-- default is not to ignore
		-- uncomment to ignore in lua:
		-- lua = true
	},
	show_prediction_strength = false,
	min_percent = 0
})
```

cmpのソースとして追加

```lua
# 表示する文字色を紫色に設定
vim.api.nvim_set_hl(0, "CmpItemKindTabNine", { fg = "#7A00FF" })

require('cmp').setup({
	snippet = {
		expand = function(args)
			luasnip.lsp_expand(args.body)
		end,
	},
	sources = cmp.config.sources({
		{ name = "copilot" }, -- GitHub Copilot
		{ name = "cmp_tabnine" }, -- Tabnine
		{ name = "luasnip" }, -- snippet
		{ name = "buffer" }, -- text within current buffer
		{ name = "path" }, -- file system paths
	}),
	formatting = {
		format = lspkind.cmp_format({
			maxwidth = 200,
			ellipsis_char = "...",
			symbol_map = {
				TabNine = "",
			},
		}),
	},
})
```

## まとめ

この記事では、NeovimでTabnineを設定し、使用する方法について説明しました。TabnineはAIを活用したコード補完プラグインで、コードの文脈を理解し、最も適切な補完候補を提案します。また、コード生成、コード検索、マルチ言語対応などの機能も提供します。

Tabnineを使用することで、コードの品質を向上させ、開発効率を大幅に改善できます。これにより、より複雑な問題に集中し、より創造的なソリューションを開発する時間が増えます。

今後もTabnineのようなツールを活用して、コーディングの効率と楽しさを最大限に引き出していきましょう。

## 参考

- [tzachar/cmp-tabnine: TabNine plugin for hrsh7th/nvim-cmp](https://github.com/tzachar/cmp-tabnine)
- [Install Tabnine for Neovim | Tabnine AI code assistant](https://www.tabnine.com/install/neovim/)
