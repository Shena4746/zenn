---
title: "latexindentの実践的な使い方&設定例"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## この記事の位置づけ・特徴

最初に, latexindent というツールそれ自体について一点だけ指摘しておくべきことがある.
**latexindent はデフォルト設定ではほとんど仕事をしないツールである** ということだ.
python でいう black のような, インストールして IDE と紐づければ後は勝手に仕事をしてくれるタイプの formatter ではない.
実際, 多くの機能はデフォルトではオフになっている. latexindent 導入の山場は, 必要な Perl モジュールのインストールではなく(あれも面倒なことは認める), 自分用の設定を書くパートにある.
有り難いことに(?), latexindent の設定項目は膨大であり, となるとやはり公式ドキュメントもそれなりのボリュームがある. それにも関わらず, 日本語の解説が非常に少ないため, ライトユーザーには厳しい状況のように思える(2023年執筆時点).

この記事は, その latexindent の使い方に関する日本語の解説記事を意図して書いた. ただし, 入門的というよりはかなり実践的な内容になっている.
[latexindent の使い方][How to use latexindent] とかで入門的な知識を仕入れた人が次に読むとだいたい分かる, くらいのレベルのはず.
(なお, これを書いている人間のバックグラウンドは数学なので, 紹介する内容もその方面に偏っている.)
"実践的"の意味するところは, ツールを現実に使う際の応用重視, というニュアンスだと思ってもらえば大体合っている.
したがって, 全くの初学者に対して優しい記事である保証はない.
また, 取り上げる話題は, 相対的に少ない労力で多くの利益が見込めるであろうものに限定しているから包括的でもない.

## デモ

この記事で紹介する適当な設定を行ったときに, latexindent がどの程度仕事をしてくれるのかを以下のデモで示す.
それを見て興味が湧かなければ, その時点でブラウザバック&サヨナラで構わない.
実際, デモに出てくる以上のことはこの記事では解説されないので.

### デモ1

![demo-format-ctn][demo-format-ctn]

```
# before
Let \(U\) be an open subset of\(\mathbb{R}    \). A function \(f:U \to \mathbb{R}\)is said to be \textit{continuous at}\( x_0\)if for any\(\epsilon>0 \)there is\( \delta>0\) such that\begin{equation}\abs{x-x_0}\implies\abs{f(x)-f(x_0)}<\epsilon.\end{equation}
```

```
# after
Let \( U \) be an open subset of \( \mathbb{R} \).
A function \( f:U \to \mathbb{R} \) is said to be \textit{continuous at} \( x_{0} \) if for any \( \epsilon > 0 \) there is \( \delta > 0 \) such that

\begin{equation}
    \abs{x - x_{0}} \implies \abs{f(x) - f(x_{0})} < \epsilon.
\end{equation}
```

- latexindent のデモ1でのお仕事
  - ピリオド `.` の後に改行を置く
  - 数式環境 `\begin \end {equation}` を独立行に移し, 中身にインデントを与える
  - インライン数式環境 `\( \)` の開始直後と終了直前に space を1つだけ置く
  - インライン数式環境 `\( \)` とその前後の文章の間に space を1つだけ置く
  - 数式環境内の(不)等号・二項演算子の周りに space を1つだけ置く
  - 隣接する `\command` の間に space を1つだけ置く
  - 数式環境内の subscript に braces `{}` を補完する: `x_0`→`x_{0}`

### デモ2

![demo-format-card][demo-format-card]

```
# before
\begin{equation}\begin{aligned}\func{\mathbb{Z}_{+}}{\mathbb{Z}_{+}}\\&\hookrightarrow \func{\mathbb{Z}_{+}}{\mathcal{P}(\mathbb{Z}_{+})}\\&\sim\func{\mathbb{Z}_{+}}{\func{\mathbb{Z}_{+}}{\Set{0,1}}}\\&\sim\func{\mathbb{Z}_{+}\times\mathbb{Z}_{+}}{\Set{0,1}}\\&\sim\func{\mathbb{Z}_{+}}{\Set{0,1}}\end{aligned}\end{equation}
```

```
# after
\begin{equation}
    \begin{aligned}
        \func{\mathbb{Z}_{+}}{\mathbb{Z}_{+}} \\
         &\hookrightarrow   \func{\mathbb{Z}_{+}}{\mathcal{P}(\mathbb{Z}_{+})} \\
         &\sim              \func{\mathbb{Z}_{+}}{\func{\mathbb{Z}_{+}}{\Set{0,1}}} \\
         &\sim              \func{\mathbb{Z}_{+} \times \mathbb{Z}_{+}}{\Set{0,1}} \\
         &\sim              \func{\mathbb{Z}_{+}}{\Set{0,1}}
    \end{aligned}
\end{equation}
```

- latexindent のデモ2でのお仕事 (既出内容は省略)
  - 数式環境 `\begin \end {equation}` と `\begin \end {aligned}` を独立行に移し, ネストしたインデントを与える
  - aligned 環境において, `\\` の位置でソースコードを改行する
  - aligned 環境において, `&\command` を一纏まりの区切り文字と認識して, それらを左揃えにする
  - 区切り文字の後に続く文字列の開始地点を左揃えにする
  - (おまけ) `\\` の位置は揃えない

### デモ3

![demo-format-exp][demo-format-exp]

```
# before
\begin{equation}\exp x\\=\sum_{n=1}^{\infty}\frac{x^n}{n!}\\=\lim_{n\to\infty}\left(1+\frac{x}{n}\right)^{n}\end{equation}
```

```
# after
\begin{equation}
    \exp x \\
    = \sum_{n = 1}^{\infty} \frac{x^{n}}{n!} \\
    = \lim_{n \to \infty} \left( 1 + \frac{x}{n} \right)^{n}
\end{equation}
```

- latexindent のデモ3でのお仕事 (既出内容は省略)
  - aligned 環境におけるような `\\` による行変更を equation 環境でもやる
  - `\left( \right)` の開始直後と終了直前に space を1つだけ置く

## 前提・予備知識

環境構築についてはほとんど書かない. latexindent の導入自体は済んでいると想定する.
latexindent 自体の予備知識としては, [latexindent の使い方][How to use latexindent] の "Example of settings for latexindent" の手前までを読んで理解しているものとする. 自信のない人は上の記事は全て読むことを勧める. 本記事とは見解の異なる部分もあるが, その違いも含めて有益だと思う.

上の記事と重複する部分もあるが, 設定ファイルの扱い方についてだけは重要項目を例示列挙しておく.

- `latexindent -vv` で, デフォルト設定が書かれた `defaultSettings.yaml` のパスが分かる
- `defaultSettings.yaml` はユーザー設定を書く際の元ネタであって, 直接編集するものではない. ユーザー設定ファイル(e.g., `.latexindent.yaml`)にデフォルト設定を選択的に引用→上書きする. ユーザー設定ファイルに書かなかった項目は勝手にデフォルトが適用されるので, 変更項目だけをユーザー設定に書けばよい.
- ユーザー設定ファイルは, ホームディレクトリに置く `.indentconfig.yaml` を経由して指定するか(Docker環境ならこれが便利), `-l` スイッチで指定する
- (Advanced) デフォルトでは, latexindent は tex ファイルだけでなく sty ファイル等にも働くことになっている. しかし, これらのファイルは互いに異なる syntax が用いられるのが通常なので, それぞれの拡張子に対して別々のユーザー設定ファイルを割り当てる方が自然. 特に, tex ファイルに対する設定と sty に対する設定は衝突しやすい.

## latexindent の最優先設定項目

独断と偏見に基づき, 以下4つを挙げる.

- `lookForAlignDelims`
- `oneSentencePerLine` (ModifyLineBreak)
- `Poly-switches` (ModifyLineBreak)
- `Replacements`

これは重要度順ではなく, 公式ドキュメントにおける出現位置順. 重要度は, 用途と latexindent 以外のツールの整備状況に依存するので一般に答えることは難しい(この中だと `oneSentencePerLine` を下に置く人が多いとは思う. 他は分からない).

これらを押さえるだけで, 他設定はデフォルトのままでも, 執筆環境の快適度の桁が上がるはず. それぞれの概要を述べておこう.

`lookForAlignDelims` は, 環境内の delimiter (たとえば, aligned 環境における `&`) と DBS(double back slash) `\\` の位置関係を中心に, 環境内の他要素の format ルールを制御する. 自作環境も含め好きな環境を登録でき, 各環境ごとに delimiter の定義をユーザーが自由に正規表現で指定できる. つまり, 色々な環境内における format ルールを柔軟に設定できる. デモでは, delimiter=`&\command` として aligned 環境の要素を左揃えにしていた. ひとまずは, tabular や align 系のように `&` と `\\` を典型的に持つ環境に対する format ルールだと思っておけばよい.

`oneSentencePerLine` は, `ModifyLineBreak` というジャンルの中の1つ. 名前の通り, 自動で文章の終わりに改行を打ってくれる.
一文の開始パターンの定義と, 終了パターンの定義を, 必要なら正規表現で指定することができる. 非インライン数式環境外のソースコードの可読性に資する.

`Poly-switches` も `ModifyLineBreak` の中の1つ. この項目内に登録した任意の環境の, 開始直前・開始直後・終了直前・終了直後のそれぞれに改行または空行を入れるかどうかを制御する. `lookForAlignDelims` が環境"内部"の format ルールであるのに対して, `Poly-switches` は環境の"境界部"の format ルールを定める.
また, `lookForAlignDelims` と連携して, DBS `\\` の位置指定(行頭 or 行末), その前後における改行有無なども指定できる. この連携により, 環境の format ルールをよりきめ細やかに制御できる. たとえば, デモにおいて, `\\` の後に改行を入れていたのはこの機能を使った.

`Replacements` は, latexindent の最も攻めた機能. ユーザーの書いた正規表現による置換を実行する. 特定の数式環境のレイアウトは `lookForAlignDelims` や `Poly-switches` で調整できるが, 数式それ自体に対する format や特定の環境外にある文字列に対する format は `Replacements` に頼ることになる. 公式ドキュメントには書いてないが, 正規表現だけでなく Perl コードを書くこともできる.
デモの中の space を1つだけ挿入する系の操作は, ほとんど全てが `Replacements` によって実行されている.

以下で, これらのざっくりとした各論と具体的な設定例を見る.

## 各論と設定例

大雑把な説明と設定例を挙げていく.
詳細な説明は[公式ドキュメント][doc latexindent]を参照のこと.

### lookForAlignDelims

公式ドキュメントの該当箇所: [5.5. Aligning at delimiters][align-at-delim].

概要は上で述べたばかりなので, そちらを参照.

#### 設定概要

`defaultSettings.yaml` の該当部分を引用すると次のようになっている.

```
# default
lookForAlignDelims:
    tabular:
        delims: 1
        alignDoubleBackSlash: 1
        spacesBeforeDoubleBackSlash: 1
        multiColumnGrouping: 0
        alignRowsWithoutMaxDelims: 1
        spacesBeforeAmpersand: 1
        spacesAfterAmpersand: 1
        justification: left
        alignFinalDoubleBackSlash: 0
        dontMeasure: 0
        delimiterRegEx: (?<!\\)(&)
        delimiterJustification: left
        lookForChildCodeBlocks: 1
        alignContentAfterDoubleBackSlash: 0
        spacesAfterDoubleBackSlash: 1
    tabularx:
        delims: 1
    longtable: 1
    tabu: 1
        .
        .
        .
```

`longtable: 1` などは簡易設定であって, これがオンオフのみを指定している. オンのときはデフォルトに従う.
`tabular` についてだけ詳細設定が例示されている. ちなみにこれがデフォルトの内容. このデフォルトを上書きしたいときだけ詳細設定を書けばよい.

詳細設定の全てを理解する必要はなく, 一旦以下を理解すればよいと思う.

|            Setting            |   Value    |                       Description                        |
| :---------------------------: | :--------: | :------------------------------------------------------: |
|           `delims`            |    0/1     |                   `&` を揃えるかどうか                   |
|    `alignDoubleBackSlash`     |    0/1     |                  `\\` を揃えるかどうか                   |
| `spacesBeforeDoubleBackSlash` |     n      |                   `\\` 前の space の数                   |
|    `spacesBeforeAmpersand`    |     n      |                   `&` 前の space の数                    |
|        `justification`        | left/right | delimiter で隔てた各セルを<br>左/右揃えのどちらにするか  |
|       `delimiterRegEx`        |   RegExp   |                     delimiter の定義                     |
|   `delimiterJustification`    | left/right | 長さの異なる delimiter 達は<br>左/右揃えのどちらにするか |

イメージしやすいように delimiter のことを `&` と書いたが, 実際の delimiter は `delimiterRegEx` においてユーザーが正規表現で指定する文字列のパターンであって, 必ずしも `&` とは限らない. たとえば, aligned 環境であれば `&` 単体よりも, `&=` や `&\simeq` などの, "`&`+二項関係記号"を delimiter として指定するのが自然だろう. なお, `delimiterRegEx: (?<!\\)(&)` と例示されているように, delimiter をキャプチャするための `()` は必須である. これを省くとエラーは起こさないが意味不明な挙動をするのでかなり厄介.

#### 設定例

デモで使った `aligned` 環境と `equation` 環境の設定例を以下で紹介する.

```
lookForAlignDelims:
    aligned:
      delims: 1
      alignDoubleBackSlash: 0   # Changed
      spacesBeforeDoubleBackSlash: 1
      multiColumnGrouping: 0
      alignRowsWithoutMaxDelims: 1
      spacesBeforeAmpersand: 1
      spacesAfterAmpersand: 1
      justification: left
      alignFinalDoubleBackSlash: 0
      dontMeasure: 0
      delimiterRegEx: (?<!\\)((?:&\\[a-zA-Z]+)|(?:&[\+\-\*=<>]?))   # e.g., &\command, &=   # Changed 
      delimiterJustification: left
      lookForChildCodeBlocks: 1
      alignContentAfterDoubleBackSlash: 0
      spacesAfterDoubleBackSlash: 0
    equation: # the same for equation*, gather and gather*
      delims: 1
      alignDoubleBackSlash: 0   # Changed
      spacesBeforeDoubleBackSlash: 1
      delimiterRegEx: 0^ # no delimiters    # Changed
```

`aligned` 環境について. 二箇所変えた.
まず `delimiterRegEx` の定義を, コメントに書いてある通り, 先述した"`&`+二項関係記号"のパターンを含むように delimiter として指定している. `&` と次のコマンドの間の spacing 次第では false-positive を生む点だけ注意.

`alignDoubleBackSlash` を `0` にしているのは, 以下の例のように, ソースコード上の表記で長い式が来たときに, その式の末尾の位置に `\\` が固定されるのを嫌ったためだ. 長い式の表示がエディタによって折り返されると短い式の末尾に大量の不要なスペースが生まれることになる.

```
\begin{equation}
    \begin{aligned}
        f(x) &= \loooooooooooooooooooooooongTerm \\
             &= \shorTerm                        \\
             &= 0
    \end{aligned}
\end{equation}
```

上に挙げた設定例の中で

```
equation: # the same for equation*, gather and gather*
    delims: 1
    alignDoubleBackSlash: 0   # Changed
    spacesBeforeDoubleBackSlash: 1
    delimiterRegEx: 0^ # no delimiters    # Changed
```

として, delimiter のない環境をあえて書いているのは, `Poly-switches` の `DBSFinishesWithLineBreak` が, `lookForAlignDelims` に挙げられている項目しか参照しない仕様になっているためだ. それに見つけてもらうために, `equation` などを delimiter は持たないが `\\` は持ち得る環境として `lookForAlignDelims` の中に挙げている. つまり, ここに `equation` を書いた主たる目的は, この field に名前が挙がっているという事実が欲しかったから.

### modifyLineBreaks

公式ドキュメントの該当箇所: [6. The -m (modifylinebreaks) switch][modify-line-break].

その名の通り, 改行の操作を行う項目. latexindent に `-m` オプションを与えることで実行される.
ただし, 例のごとく, デフォルトでは以下のように既存の改行は消さない設定になっているので, あまり仕事はしない.

```
# default
modifyLineBreaks:
    preserveBlankLines: 1
    condenseMultipleBlankLinesInto: 1
```

次以降の節で見る `oneSentencePerLine` と `Poly-switches` では, `preserveBlankLines: 0` を前提として書く.

### oneSentencePerLine

公式ドキュメントの該当箇所: [6.2. oneSentencePerLine: modifying line breaks for sentences][one-sentece-line].

上で述べた概要を再掲.
>`oneSentencePerLine` は, `ModifyLineBreak` というジャンルの中の1つ. 名前の通り, 自動で文章の終わりに改行を打ってくれる.
>一文の開始パターンの定義と, 終了パターンの定義を, 必要なら正規表現で指定することができる. 非インライン数式環境外のソースコードの可読性に資する.

#### 設定概要

`defaultSettings.yaml` の該当部分を引用すると次のようになっている.

```
# default
modifyLineBreaks:
    oneSentencePerLine:
        manipulateSentences: 0              # 0/1
        removeSentenceLineBreaks: 1         # 0/1
        multipleSpacesToSingle: 1           # 0/1
        textWrapSentences: 0                # 1 disables main textWrap
        sentenceIndent: ""
        sentencesFollow:
            par: 1                          # 0/1
            blankLine: 1                    # 0/1
            fullStop: 1                     # 0/1
            exclamationMark: 1              # 0/1
            questionMark: 1                 # 0/1
            rightBrace: 1                   # 0/1
            commentOnPreviousLine: 1        # 0/1
            other: 0                        # regex
        sentencesBeginWith:            
            A-Z: 1                          # 0/1
            a-z: 0                          # 0/1
            other: 0                        # regex
        sentencesEndWith:
            basicFullStop: 0                # 0/1
            betterFullStop: 1               # 0/1
            exclamationMark: 1              # 0/1
            questionMark: 1                 # 0/1
            other: 0                        # regex
```

主要項目だけ触れておく.

- `manipulateSentences: 0` → `oneSentencePerLine` のオン/オフ設定. デフォルトではオフ
- `removeSentenceLineBreaks: 1` → `oneSentencePerLine` が既存の改行を消去することを許可するかどうか. デフォルトはYES
- `multipleSpacesToSingle: 1` → 連続した space を1つまとめるか. デフォルトはオン.
- `sentencesFollow:` → 文と評価し得る文字列の前に出現するべき記号. `exclamationMark: 1` なら, `!` の後に続く文章は文として評価対象になる.
- `sentencesBeginWith:` → 文の開始パターン. `other` filed に正規表現を書くことでルールを追加可能.
- `sentencesEndWith:` → 文の終了パターン.
  - `betterFullStop: 1` とすると, `e.g.,` などの略語中のピリオド `.` や特定パターンを後続に持つピリオドを一文の終了箇所とは評価しない.

#### 設定例

文章の癖に強く依存する項目なので, 人のものを真似るというより, 自分に合わせて育てていくべきだと思う.
以下は, 数学等で `!` を階乗の意味で使うことを念頭に置いたもの.

```
modifyLineBreaks:
    oneSentencePerLine:
        manipulateSentences: 1              # 0/1
        removeSentenceLineBreaks: 1         # 0/1
        multipleSpacesToSingle: 1           # 0/1
        sentencesBeginWith:            
            A-Z: 1                          # 0/1
            a-z: 0                          # 0/1
            other: ^\\\(                    # regex # Changed
        sentencesEndWith:
            basicFullStop: 0                # 0/1
            betterFullStop: 1               # 0/1   # Changed
            exclamationMark: 0              # 0/1
            questionMark: 1                 # 0/1
            other: \\linebreak              # regex # Changed
```

この設定下では, 強調等の意味で `!` を文末に使いたいときは, `!\\linebreak` と書くことになる.
文頭のインライン数式も一文の開始位置ということに設定した.

### Poly-switches

```
# default
modifyLineBreaks:
        .
        .
        .
    environments:
        BeginStartsOnOwnLine: 0
        BodyStartsOnOwnLine: 0
        EndStartsOnOwnLine: 0
        EndFinishesWithLineBreak: 0
        equation*:
            BeginStartsOnOwnLine: 0
            BodyStartsOnOwnLine: 0
            EndStartsOnOwnLine: 0
            EndFinishesWithLineBreak: 0
```

### Replacements

```
# example from official doc 
replacements:
  -
    substitution: s/\h+/ /sg
    lookForThis: 1
    when: after
```

### Further Readings

## 参考文献

- [How to use latexindent][How to use latexindent]
- [latexindent.pl 3.22.2 documentation][doc latexindent]
- [A Template for Docker + TeXLive + VSCode + Remote-Container + LaTeX-Workshop][GitHub my-template-texlive]

[How to use latexindent]:https://gist.github.com/Yarakashi-Kikohshi/8c39707f83ba73b3bce73c54638ac594
[doc latexindent]:https://latexindentpl.readthedocs.io/en/stable/
[GitHub my-template-texlive]:https://github.com/Shena4746/template-texlive-docker-vscode
[demo-format-ctn]:/images/practical-latexindent/demo-format-ctn.gif
[demo-format-card]:/images/practical-latexindent/demo-format-card.gif
[demo-format-exp]:/images/practical-latexindent/demo-format-exp.gif
[align-at-delim]:https://latexindentpl.readthedocs.io/en/latest/sec-default-user-local.html#aligning-at-delimiters
[one-sentece-line]:https://latexindentpl.readthedocs.io/en/latest/sec-the-m-switch.html#onesentenceperline-modifying-line-breaks-for-sentences
[modify-line-break]:https://latexindentpl.readthedocs.io/en/latest/sec-the-m-switch.html#
