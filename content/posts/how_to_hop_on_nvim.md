+++
title = "[Neovim]hop.nvimで爆速移動"
date = "2024-07-16T16:50:23+09:00"
author = "kseki"
cover = "/img/hop-cover.png"
tags = ["Neovim", "Neovimプラグイン"]
categories = ["開発", "効率化"]
description = ""
+++

## 概要

hopプラグインはカーソル移動のプラグインです。できるだけ少ないキー操作での任意の場所にジャンプできるようになります。

## 設定

プラグイン管理がLazyの場合は、以下のように設定できます。

```lua
return {
	"smoka7/hop.nvim",
	event = "BufRead",
	version = "*",
	opts = {
		multi_windows = true,
	},
	keys = {
		{ "<leader>w", "<cmd>HopWord<CR>", mode = "n", desc = "Hop Word" },
		{ "<leader>l", "<cmd>HopLine<CR>", mode = "n", desc = "Hop Line" },
		{ "<leader>c", "<cmp>HopChar1<CR>", mode = "n", desc = "Hop Char" },
		{ "<leader>p", "<cmd>HopPattern<CR>", mode = "n", desc = "Hop Pattern" },
		{ "f", "<cmd>HopChar1CurrentLineAC<CR>", mode = { "n", "v", "o" }, desc = "Hop Char in Line (After Cursor)" },
		{ "F", "<cmd>HopChar1CurrentLineBC<CR>", mode = { "n", "v", "o" }, desc = "Hop Char in Line (Before Cursor)" },
		{
			"t",
			"<cmd>lua require'hop'.hint_char1({ direction = require'hop.hint'.HintDirection.AFTER_CURSOR, current_line_only = true, hint_offset = -1 })<CR>",
			mode = { "n", "v", "o" },
			desc = "Hop Before Char in Line (After Cursor)",
		},
		{
			"T",
			"<cmd>lua require'hop'.hint_char1({ direction = require'hop.hint'.HintDirection.BEFORE_CURSOR, current_line_only = true, hint_offset = 1 })<CR>",
			mode = { "n", "v", "o" },
			desc = "Hop After Char in Line (Before Cursor)",
		},
	},
}
```

## 使い方

### 画面上の特定の単語の先頭にジャンプしたい時

`HopWord`

### 

`HopWord`

## 参考
- [smoka7/hop.nvim: Neovim motions on speed!](https://github.com/smoka7/hop.nvim)
