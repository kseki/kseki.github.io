---
title: TypeScriptでフィッシャーイェーツのアルゴリズムを使って配列をシャッフルする方法
description: TypeScriptでフィッシャーイェーツのアルゴリズムを使って配列をシャッフルする方法を解説します。Rubyの実装例も紹介。
keywords: 
    - Ruby
    - TypeScript
    - 配列シャッフル
    - アルゴリズム
    - フィッシャーイェーツ
    - Fisher-Yates
slug: shuffle-algorithm-by-typescript
date: 2024-08-06T16:33:05+09:00
image: cover.webp
categories:
    - 開発
    - 言語
tags: 
    - アルゴリズム
    - TypeScript
    - Ruby
weight: 1
draft: false
---

## 概要

先日、あるプロジェクトで配列の要素をランダムにシャッフルする必要がありました。TypeScript (5.5.4) には組み込みの `shuffle` 関数がないため、自分で実装する必要がありました。この記事では、その際に調べたフィッシャーイェーツのシャッフルアルゴリズムについて解説します。

## Rubyの配列シャッフル実装

RubyにはArryクラスに[suffle](https://github.com/ruby/ruby/blob/v3_3_4/array.rb#L16-L28), [suffle!](https://github.com/ruby/ruby/blob/v3_3_4/array.rb#L2-L14) という関数があります。

これらの関数は、以下のC言語で書かれている[rb_ary_suffle_bang](https://github.com/ruby/ruby/blob/v3_3_4/array.c#L6496C1-L6516C2) 関数を呼び出しています。

この関数のアルゴリズムは[フィッシャーイェーツのシャッフル](https://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A3%E3%83%83%E3%82%B7%E3%83%A3%E3%83%BC%E2%80%93%E3%82%A4%E3%82%A7%E3%83%BC%E3%83%84%E3%81%AE%E3%82%B7%E3%83%A3%E3%83%83%E3%83%95%E3%83%AB)と言われるものです。特徴は以下の通りです：
- **ランダム性**: 配列の各要素が等しい確率で任意の位置に移動する。
- **インプレース**: 配列の要素をその場でシャッフルし、追加のメモリをほとんど使用しない。
- **時間計算量**: O(n)の時間でシャッフルを完了する。
- **安定性**: 元の順序を保持しないため、安定なソートではない。
- **シンプルな実装**: アルゴリズムはシンプルで理解しやすい。

```c
static VALUE
rb_ary_shuffle_bang(rb_execution_context_t *ec, VALUE ary, VALUE randgen)
{
    long i, len;

    rb_ary_modify(ary);
    i = len = RARRAY_LEN(ary);
    RARRAY_PTR_USE(ary, ptr, {
        while (i) {
            long j = RAND_UPTO(i);
            VALUE tmp;
            if (len != RARRAY_LEN(ary) || ptr != RARRAY_CONST_PTR(ary)) {
                rb_raise(rb_eRuntimeError, "modified during shuffle");
            }
            tmp = ptr[--i];
            ptr[i] = ptr[j];
            ptr[j] = tmp;
        }
    }); /* WB: no new reference */
    return ary;
}
```

## TypeScriptで配列シャッフル実装


この関数は、配列の要素をランダムにシャッフルし、元の配列を変更します。フィッシャーイェーツのアルゴリズムを使用しているため、効率的かつ簡単に実装できます。
```tsx
const shuffleArray = (array: string[]) => {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
  return array;
}
```

## おみくじの実装例

上記の関数を使っておみくじを実装してみました。

[フィッシャーイェーツ関数でおみくじ | Playground Link](https://www.typescriptlang.org/play/?#code/MYewdgzgLgBBAWBXAZsgNgUwIICccEMBPGAXhgAp88iAuOKHASzAHMBtAXQEpSA+GAN4AoGDGQgcFTLEakYVAoQB0mVlHgwAtDACMAbhiz+ABgONNmnsNGjQkWACs5AWXzql6EBPKv3BMAAmIAC25DwAVBSyANS6XFx6IjZsCkRsjBwANPLUhGwOHBxyKbn5WTmK6YWJogC+STgYUIg4YBVEibWJQnbQMCGMANaIDrJkbEmiAOSA5JqAkCpTmZMwU4C0cgtLNiuA8Doby1N7W1OAUsqHNlOA1OZn0xe7i-uAb4r3R9tPm+fHb-sXX0czT0IOEIeuAICBMCoQCxyAgUOhsLlyANhqMuGxjNwgA)
