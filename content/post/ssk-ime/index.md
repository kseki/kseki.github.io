---
title: 日本語入力が効率的になるというSSKを試してみた
description: 日本語入力が効率的になるSSKを試してみた
slug: hello-world
date: 2022-03-05T23:04:37+09:00
categories:
    - OS
tags:
    - 日本語入力
    - IME
    - 効率化
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## SKKとは

- SKK（エスケイケイ、Simple Kana to Kanji conversion program）
- Emacs上で動く、日本語入力システムの1つである

引用: [SKK \- Wikipedia](https://ja.wikipedia.org/wiki/SKK)

### 既存IMEの問題点

- 形態素解析に基づいた自動変換で、文節を間違っていた場合にはカーソルキーで文節を変更する必要がある。その為、余計なキータイプが発生する。

### SKKの特徴

1. かな漢字変換において形態素解析に基づいた変換を行わない。かなと漢字の境界をユーザが指定する。
2. ホームポジションを崩さず入力
3. シームレスな辞書登録

引用: [SKK \- Wikipedia](https://ja.wikipedia.org/wiki/SKK)

## AquaSKK

今回試したのは、AquaSKKというMacOSでもつかえるSKKベースのアプリケーションです。

[Releases · codefirst/aquaskk](https://github.com/codefirst/aquaskk/releases)

## SKKの使い方

- 基本的にローマ字入力→ひらがなが一文字ずつ確定入力される(エンターいらず)
- 漢字変換するには、一文字目を大文字入力する必要がある
  - 送りがななしのケース例： `Kanji`と入力し、スペースキーで候補選択、次のキータイプ or エンターキーで確定する
  - 送りがなありのケース例： `OkuRigana`と入力し、スペースキーで候補選択、次のキータイプ or エンターキーで確定する
  - 候補の漢字が無いケース: 自動的に辞書登録モードになる
- カタカナ入力するには、一文字目を大文字入力し、qキーを押す必要がある
  - 例： `Katakana`と入力し、qキー入力でカタカナになり、同時に確定する

### 良い所

- キータイプ数が減る
- エンターキーを本来の目的のみで使える
- 即辞書登録できるのは良い
- 慣れると手書きと同じ感覚で使えるらしい

### 悪い所

- 今までの癖を抜くため相当な慣れが必要
- シフトキー多用するので小指が疲れる

