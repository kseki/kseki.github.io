+++
title = "NeovimでTabnineを使う方法"
date = "2024-07-02T16:38:00+09:00"
author = "kseki"
cover = ""
tags = ["Neovim", "AI"]
categories = ["開発", "効率化"]
description = ""
showFullContent = false
readingTime = false
+++

### Tabnineとは

Tabnineは、AIを活用したコード補完プラグインです。従来の補完機能よりも高度な機能を提供し、コード開発を効率化します。

主な機能は以下の通りです。

- **コンテキストに応じた補完:** コードの文脈を理解し、最も適切な補完候補を提案します。
- **コード生成:** 関数やクラスの定義、テストコードなど、様々なコードを自動生成することができます。
- **コード検索:** コード内の変数や関数の定義箇所を素早く検索することができます。
- **マルチ言語対応:** 多くのプログラミング言語に対応しており、様々な言語間で補完やコード生成を行うことができます。

### 3. Neovimにインストールする方法

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

cmp.set
cmp.setup({
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

### 5. まとめ

Tabnineは、AIによるコード補完によって、プログラミングの効率を大幅に向上させることができるツールです。Neovimでのセットアップも比較的簡単であり、プログラミング作業をよりスムーズに行いたい方には非常におすすめです。

### 参考

- [tzachar/cmp-tabnine: TabNine plugin for hrsh7th/nvim-cmp](https://github.com/tzachar/cmp-tabnine)
- [Install Tabnine for Neovim | Tabnine AI code assistant](https://www.tabnine.com/install/neovim/)
