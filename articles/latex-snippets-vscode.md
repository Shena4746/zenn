---
title: "数式爆速入力のための LaTeX 動的スニペット in VSCode"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["latex","vscode"]
published: true
---

# 忙しい人向けの要約

- [HyperSnips Extension][hsnip-ext] っていう VSCode 拡張機能を使うと数式入力をさらに早くできるよ
- VSCode デフォのユーザースニペットや LaTeX-Workshop のような定型文入力ではなく, 正規表現を用いた入力済文字列に対する加工を行う動的なスニペットが作れるよ
- そのスニペットとトリガーの ON/OFF を数式環境にいるかいないかで自動切替えできるよ
- tex ファイル向けの設定を正規表現と JavaScript で色々書く必要があるけど, すぐに導入可能な既存の公開品があるよ([実践のお供](#実践のお供)参照)
- この記事の元ネタはこれだよ→ [GitHub][shena-snippets]

# 読み方の選択肢

- その1 じっくり: 前から順番に読む
- その2 速習: 言葉の定義→デモ1→動的スニペットの定義以降と読む
- その3 実践: 言葉の定義→デモ1→実践のお供だけ読んで早く使ってみる

# インストール

VSCode の拡張機能をインストールして, 拡張機能の説明通りにファイル(latex.snips)を配置するだけ.

:::message
latex.snips の中身は自分で追加する必要があります. デフォルトではほとんど何も提供されていません.
:::

# 言葉の定義

非常に大雑把に以下のようにスニペットの分類をしておきます. 単にスニペットと書いたときに, 多くの人は次の定義の前者を想像してしまうため, 違いを強調するための呼称です.

:::message

- 静的スニペット: VSCode の [user-snippets][page-snippets-in-vscode] に JSON 形式で文字列を登録して使う定型文スニペット
- 動的スニペット: [HyperSnips Extension][hsnip-ext] を使って定義したスニペット
:::

# 背景

よくある VSCode の静的スニペットによる数式入力速度向上に限界を感じて, ここ数年 [HyperSnips Extension][hsnip-ext] を使った動的スニペットをコソコソ使っていました.
それなりの期間使用してみて, その効果を実感できていること(静的スニペットは`\begin \end`型の環境を出す以外に使わなくなった), 私の個人的用途におけるスニペット整備が概ね完了して更新が停滞しこと, チマチマ書いてたチートシートやドキュメントがそれっぽい形になったこと, 最近静かだった拡張機能の更新が先日来て少なくとももうしばらくは使えるという予感を得たことなどにより, 公開することにしました. 私の周りでこれを使っている人が少ないので, 布教もかねています.

# デモ

使用シーンを見た方がイメージが湧くと思うので, あらゆる説明をすっ飛ばして, 動的スニペットのデモを載せます.
一旦の理解として, 動的スニペットなるものは, VSCode 標準のスニペットや LaTeX-Workshop のスニペットではない, 正規表現ベースのスニペットだと思ってください.

ただ, 動画だけ見ても意味不明だと思うので, キーストロークも合わせて書いておきます.

- キーストローク内の表記
  - `|` = Tab
  - `D` = Delete
  - `E` = Enter
  - `C` = Ctrl (特に重要ではない)
  - `{}`: 単語の区切りを見やすくするために敢えて書いたもの. 実際は入力されていない.
  
以下のデモにおいて, `eqv` **だけ**が静的スニペットで, それ以外のスニペット(`#` とか `;;` とか `zd`)は HyperSnips Extension で定義した動的スニペットです. これらは周辺の文字に自動的に作用する関数のように振る舞います.

## デモ1: 実関数の連続性の定義を書く

`Let U{;;}|be an open subset of {;;}{R#}|.CE A function {;;}f:U to {R#}|is said to be {tit}contnuous at|{;;}{x0}|if for any {;;}{ze}>0|there is {;;}{zd}>0|such that {eqv}{E}{abs}x-x0|<{zd}{imp}{abs}f(x→-f(x0→<{ze}|.`Ctrl+S.

![demo1-gif][demo-continuity]
![demo1-tex][demo-continuity-tex]

冒頭の `;;` は直前の文字(U)をインライン数式の中に入れて cursor を数式環境の外に置いていますが, 別のところでは `;;` は空のインライン数式環境を作って cursor をその中に置いています. このように文脈に応じて同じ文字列のスニペットに異なる意味を持たせることができます.

R に後ろから作用させた `#` は `\mathbb` を作り出す関数(のようなもの. 以下同様)です. `x0` の `0` (正確には2桁以下の数字) も同様で, 直前の項の下付きの添字を作ります. `ze` は `z` が `e` に作用しているイメージで, アルファベットをギリシャ文字に移す関数のように振る舞っています. つまり, ここでの出力は `\epsilon` です.

都合が良いことに, `A function {;;}f:U to {R#}` の 中では `to` は `\to` に変換されていますが, 他方で, "is said to be" の中では `\to` とは変換されていません. これはとても重要な性質なので, 以降のセクションで詳しく扱います.

## デモ2: 双対基底を書く

`Let {;;}e1{,,}{D}n|be a basis of a vector space V{;;}|and let ;;{lr)}{f1}{tt}{,,}{D}n|be the...`

![demo2-gif][demo-dual-basis]

![demo2-tex][demo-dual-basis-tex]

`e1` は上で書いたように `e_1` に化けます. それに作用させた `,,` は直前の項を `, \ldots,` を挟んで繰り返した文字列を返す関数です. よって, 出力は `e_1, \ldots, e_1` です.

`lr)` は `lr` が関数名で `)` が引数だと思ってください. これは引数の `\left \right` 版を返します. つまり, 出力は `\left( \right)`. この関数で色々な `\left \right` 達を出力できます.

`f_1` に作用している `tt` は直前の項の下付き添字を上付き添字に変換する関数です. よって, `f_1tt` は `f^1` を返します.

## デモ3: 指数関数のテイラー展開を書く

`{eqv}{E}{exp}x= {sum}{^}{n}|||{xNN}{tt}|/n!|{\\}{E}={lim} {_}{n}|| {lr)}1+x/n|{^^}n`

![demo3-gif][demo-taylor-expansion]

![demo3-tex][demo-taylor-expansion-tex]

`exp` や `sum` は `\exp` や `\sum` に問答無用で化けます. この手の短い名前のコマンドは `\` を除いたコマンド名で出てきます.

`\sum` 直後の `^` は, `\sum` や `\int` 等の典型的に `_` と `^` を後ろに持つコマンドに対してそれらの内容提案をする関数です. デフォルトでは `\sum` に対しては `_{i=1}^{\infty}` を提案するように過去の私が定義していたので, `\sum ^` → `\sum_{i=1}^{\infty}` と移ります.
2つ目の等号後にいる `\lim` に対する `_` も同種の提案関数です. ちなみに `\sum` に対して `_` を作用させると `\sum_{i=1}` のように控えめな提案をしてくれます.

`\sum` の中身に戻ります.
`xNN` は `x0` のアルファベット版です. ただし, `N` ではなく小文字の `n` がつきます: `x_n`.
`/` も関数で, 直前の項を分子に持つ `\frac` を返します.

最後の `^^` は引数を取らず単に `^{}` に化けます. 直前の項が `\sum` 等ではないので, `^` は提案関数としては働かず, ここでは `^{}` へのショートカットのように働いています.

デモは以上です.

なお, これらは私が定義した(または先人から拝借した)スニペットの挙動であって, HyperSnips Extension がデフォルトでこのようなスニペットを定義しているわけではありません.
あくまで私のスニペットの挙動を, 動的スニペットのイメージ作りのために紹介しただけです.

# Math Context なるもの

動的スニペットそれ自体の説明に移る前に, 動的スニペットのトリガーの重要なパーツについて説明します.

デモ1 の最後で, `\to` コマンドに変換される `to` と変換されずにそのまま残る "to" の存在を確認しました. これらを分ける仕組みについて説明します. 結論から言えば, 入力地点が数式環境内か否かを判定する(ために必要な情報を含む)変数が, HyperSnips Extension では利用可能です. `context` という変数です.

大雑把に言えば, `context` は現在位置の cursor の周辺情報を持つ変数です. 数式環境内に cursor があるとき, `context` は数式環境内特有の値を持ちます. この値を常に監視して, 数式環境内にいると分かれば "to" を `\to` に変換するトリガーを ON にして, そうでないときは OFF という条件分岐が裏では設定されていたわけです.

静的スニペットのトリガーがファイル上でグローバルに有効であるのとは対照的に, 動的スニペットのトリガーは `context` 変数を通してローカルに定義されているという見方もできます. トリガーが有効化される範囲が有意義に限定されているからこそ, `to` を問答無用で `\to` に変換すると言った"攻めた"スニペットが有効に働くわけです.

デモ3の解説で書いた

> `exp` や `sum` は `\exp` や `\sum` に問答無用で化けます. この手の短い名前のコマンドは `\` を除いたコマンド名で出てきます.

という文を見て, 「常に `to` → `\to` と変換されたら数式環境外の文を書くときにめちゃめちゃ邪魔になるのでは??」と, ギョッとした人もいたかと思います. それは健全なリアクションで, これは実は暗に`context`変数の値で条件づけた話をしていたわけです.

便利なので, 次のように math context なる言葉をざっくり定義しておきます.

:::message
`context` 変数が数式環境特有の値をとる地点を **math context** という.
:::

念のため, 実際にどこが math context で, どこがそうでないのか, 以下のデモで確認してみましょう. math context を判定するスニペットを定義するのが面倒だったので, ここでは簡易に "to" が `\to` に化けるか否かで math context の判定に代えていますが, 実用上はこれでほぼ十分です.

![demo-math-mode][demo-math-mode]

"to" が `\to` に化けるかどうかは, 数式環境の内側と外側でハッキリと分かれることが確認できました. 入力箇所が数式環境の内側か外側かどうかという情報は, `context` 変数から判定できることを示す間接的な結果が得られたことになります.

蛇足ですが, `context` 変数を用いて, comment context や text context なども定義可能です.

# 動的スニペットの定義: latex.hsnips の中身

私の latex.snips に書かれているものを題材にして, 実際に動的スニペットがどう定義されているかを説明します. 自明な例から始めましょう.

## サンプル1: インライン数式の挿入

```
snippet ;; "[HS] math inline" A
\( $1 \) $0
endsnippet
```

**1行目**: `snippet` で, スニペットを定義することを宣言し, 次にトリガーを`;;`と定義しています. quotation `"` 間はスニペット名, `A` はフラグです. これは auto の略で, スニペットがユーザーの確認なしに自動展開されることを指定しています.
**2行目**: スニペットの body を定義しています. インライン数式環境 `\( \)` を挿入し, 1つ目のTabStopをその中央に, 最後のTabStopを環境外に置いています. 静的スニペットの定義方法とよく似ていますね.
**3行目**: このスニペットの定義を以上で完了することを示す `endsnippet`.

このスニペットの動作は, 冒頭のデモの各所で確認できます.

自動展開フラグ`A`を除けば, 明らかに静的スニペットでも同じものが作れます. 逆に, 静的スニペットで定義できるものは, このように動的スニペットとしても定義できます. その意味で動的スニペットは静的スニペットの拡張になっています.

## サンプル2: 上付き添字用の braces `{}` の挿入

```
context math(context)
snippet `--|\^\^` "[HS] Superscript" A
^{$1} $0
endsnippet
```

**1行目**: `math(context)` という関数が True のときだけ次は有効であるとする, という宣言です. この宣言の後に続くのはスニペット定義に限られるので, これは実質的にスニペットのトリガーに対する条件定義です. `math(context)` なる関数は別途グローバルに定義しています(最終セクション参照). これが先にざっくり述べた math context の正体であり, 動的スニペットを math context によって制御する syntax です.
**2行目**: トリガー部分を back-quote (\`) で囲むと正規表現が使えるようになります. ここでは文字列`--`または`^^`をトリガーとして設定しています. math context で条件付けているので, これは数式環境でのみ有効なトリガーを意図しています.
**3行目**: 以降は先と同様.

このスニペットは, デモ3の最後で使ったものです.

## サンプル3: \mathbb{} への変換作用 `#`

```
# R# -> \mathbb{R}
context math(context)
snippet `\b([A-Z])#` "[HS] Math bb" A
`` rv = `\\mathbb{${m[1]}}` ``
endsnippet
```

**1行目**: コメントなので省略.
**2行目**: 先と同様に, 数式環境でのみ有効なスニペットである, という宣言.
**3行目**: 単語の先頭にある大文字アルファベットの直後に `#` が付いたものをトリガーとして定義しています.
**4行目**: スニペットの body において, ダブル back-quote (\`\`) で囲った部分では JavaScript コードが使えます. `rv` はこのダブル back-quote 環境で使える変数で, この変数の中身がスニペットの出力となります. ダブル back-quote 環境の中の シングル back-quote (\`) で囲まれた部分は文字列を書くための quotation マークの代わりで(拡張機能の説明に書いてないけどそうっぽい), `${m[1]}` によって配列 `m[1]` の値を評価させています. `m` は `rv` のようにダブル back-quote 環境でのみ有効な変数で, 正規表現にマッチした文字列を格納する配列です. 文字列の中で変数を評価しないなら, その文字列はシングル quotation `'` などで囲っても大丈夫です.

このスニペットの動作は冒頭のデモ1の1行目で確認できます.

## 小括

デモの冒頭で, 動的スニペットは,

>周辺の文字に自動的に作用する関数のように振る舞います.

と書きましたが, その中身は, 関数のように振る舞ってくれるように条件を制御して書いた正規表現とJSコードの組み合わせです. そのトリガーは, 正規表現で設定できるだけでなく, `math(context)` 関数の真偽値を事前審査条件として設定できます. これによって, 数式用のスニペットは数式環境内でだけ働くように動作条件を限定できます. つまり, 数式環境を境界として, アクティブなスニペットの集合を自動的に切り替えることができます.

# 実践のお供

ここまでの内容が理解できれば, 自分の用途に合わせて動的スニペットを自作し, 入力効率の向上を図れるはずです. その際, 全てを自作する必要はないので, 有り物を活用するとよいでしょう. 私の [GitHub レポジトリ][shena-snippets]に, いくつかの先行事例と私の設定例がまとめてあるので, 参考にどうぞ.

今すぐ使いたい人は, [latex-setup][latex-setup] にあるものを使うか, または私の使ってる以下の3点セットをお勧めします.

- [latex 用の設定ファイル][my-hsnips]
- [チートシート][cheatsheet]
- [スニペットの doc][snippets-doc]

ただし, 私の設定ファイルをそのまま使う場合は, 以下に書くように `$` ベースのインライン数式環境と相性が悪いので, ご注意を.

# `math(context)` についての補足: `$` の排除

`math(context)` 関数は [HyperSnips Extension][hsnip-ext] の Overview に書いてある以下の定義を使う人が多いようです.

```
global
function math(context) {
    return context.scopes.some(s => s.startsWith("meta.math"));
}
endglobal
```

つまり, 現在位置の `context.scopes` (これは文字列の配列) が `"meta.math"` で始まるメンバーを含むとき, その位置は数式環境であると判定させています. これは数式環境の判定条件としては大らかで, false-positive を多く含みます. なぜなら, 数式環境の外側でも `"meta.math"` から始まるスコープが有りうるからです. 実際に VSCode の `Developer: Inspect Editor Tokens and Scopes` で調べてみるとその実例が見つかります(ただし, これは個々人の環境によって微妙にブレるかもしれません).

そのため, 私はもう少し厳し目の判定条件を `math(context)` に課して false-positive を減らしています. その副作用として false-negative も増え, 結果として `$` ベースのインライン数式環境を弾くことになりました. この判断の背景には, 私が元々 `$` ベースの数式環境は使っていなかったという事実も影響しています.

このように, 少なくとも現時点では, `math(context)` の実装は自然に一意に定まるものではなく, 個々人の都合とそしてスニペットの定義を考慮して**決める**ものです. その選択肢は複数あります. もし奇妙な挙動に出会ったら, `Developer: Inspect Editor Tokens and Scopes` でスコープを調べて, その結果に応じて `math(context)` を修正することをお勧めします.

# 参考文献

- [HyperSnips Extension][hsnip-ext]
- [page-snippets-in-vscode][page-snippets-in-vscode]
- [latex-setup][latex-setup]
- [これの元ネタの GitHub レポジトリ][shena-snippets]

[hsnip-ext]:<https://marketplace.visualstudio.com/items?itemName=draivin.hsnips>
[page-snippets-in-vscode]:https://code.visualstudio.com/docs/editor/userdefinedsnippets
[shena-snippets]:https://github.com/Shena4746/latex-snippets-for-vscode
[cheatsheet]:https://github.com/Shena4746/latex-snippets-for-vscode/blob/main/cheatsheet.md
[snippets-doc]:https://github.com/Shena4746/latex-snippets-for-vscode/blob/main/snippets-reference.md
[my-hsnips]:https://github.com/Shena4746/latex-snippets-for-vscode/blob/main/.vscode/latex.hsnips
[demo-continuity]:/images/latex-snippets/demo-continuity.gif
[demo-dual-basis]:/images/latex-snippets/demo-dual-basis.gif
[demo-taylor-expansion]:/images/latex-snippets/demo-taylor-expansion.gif
[demo-continuity-tex]:/images/latex-snippets/demo-continuity.png
[demo-dual-basis-tex]:/images/latex-snippets/demo-dual-basis.png
[demo-taylor-expansion-tex]:/images/latex-snippets/demo-taylor-expansion.png
[demo-math-mode]:/images/latex-snippets/demo-math-mode.gif

[latex-setup]:<https://github.com/gruvw/latex-setup>
