---
title: "コマンド化の判断基準"
---

当然ながら方針は以下の通り.

- コマンド化する・しないの基準
  - 可読性・保守性に寄与しないならコマンド化しない
  - この両方に寄与するならコマンド化する
  - 一方を犠牲にして他方を立てるケースは個別判断

これらの類型に当てはまる典型例を挙げる. 中には, 上で議論した良いコマンドの条件に必ずしも当てはまらないが, コマンド化した方がよい例もある.

なお, 以下は重要度順等ではなく, 説明する上で都合がよい順.

## 肯定的基準

### 記号変更の可能性があり, 登場回数の多い概念があるとき → する

このような概念はコマンドという単位で管理することを考えた方がよい. 可読性が増すかは現状の記号次第だが, 記号の変更が起きてしまったときの保険として有用な場合が多い.

たとえば, xxx空間をたくさん扱っていて, 大文字の記号 $X$, $Y$,...etc. が入り乱れているような状況を考えよう. このようなときは, 空間概念ごとに, たとえば `\BanachSp{X}`, `FrechetSp{V}` などのようにコマンドで仕切っておくとよい. 記号変更が起きたとき, たとえば Frechet 空間だけ `mathcal`に変えたくなったとき等にとても助かる.

### 1つの記号が用途によって異なる概念を指すとき → する

先のケースの特殊版に当たる.

同じ記号を共有する概念のうちのどれか1つに記号変更が生じた場合, `document` 環境での置換作業が非常に面倒になる. 記号用途の重なりが発生した時点で, 少なくともその片方は, 現時点の出現回数を問わずコマンド化した方がよい.

たとえば, 集合 $A$ の内部を `A^{\circ}` $A^{\circ}$, 関数の合成を `g \circ f` $g \circ f$ と書いているなら, 可読性への寄与を考慮して, 前者を `\interior{A}` として定義した方がよいだろう.
もちろん, 同時に後者を `g \composite f` などとコマンド化してもよい.

### 1つのコマンドの制限が別の対応概念を持つとき → する

典型的には, 用途の広いパッケージの関数を `document` 環境にベタ書きするときに発生しやすい.

たとえば, derivative パッケージの `pdv` や diffcoeff パッケージの `\diffp` は, 導関数 $\frac{\partial f}{\partial x_1}$ , 微分作用素 $\frac{\partial}{\partial t}$, 接空間の基底 $\frac{\partial}{\partial x_i}$...etc., という風に複数概念を表現できてしまう.

このような場合は, パッケージ関数を特殊化・制限して, 対応概念ごとにコマンド化した方が可読性も増す上に Notation の管理もしやすくなる. たとえば, `\derivative`, `\diffOpt`, `\tanBasis` のようなコマンドを作るとよいだろう.

### 合成コマンドが明確な対応概念を持つとき → する

コマンドの合成の乱用は依存関係を複雑にして, 保守性を下げる場合があるので慎重になるべきということはまず明言しておく.
しかし, このような合成の需要は, 主に可読性の向上等を目的として多々発生することも事実だ.
これを概ね安全に行える類型として, ミニマルに定義されたコマンド同士の合成が再び対応概念を持つケースが挙げられる.

たとえば, 接空間の基底の変換則が(Einstein の縮約ルールを使って)以下のように書かれているとする.

$$ \left .\frac{\partial}{\partial x_{i_1}}\right|_{p} = \frac{\partial \widetilde{x}_{j_1}}{\partial x_{i_1}}(p) \left .\frac{\partial}{\partial \widetilde{x}_{j_1}}\right|_{p} $$

そして, 右辺に対応するコードは

```tex
\derivative{\~{x}_{j_1}}{x_{i_1}}(p) \tanBasis{\~{x}_{j_1}}[p]
```

と書かれていたとする. この部分は変換則そのものに対応する

```tex
\tanBasisTrans{from=x_{i_1}, to={\~{x}}, subscr=j_1}[p]
```

のようなコマンドを定義して置き換えると可読性が上がり, 意味も明確になる. その実装は, 既存のコマンドを併記するような内容になるので, 直観的でメンテナンスが楽な内容に収まりやすい.

ただし, 可読性の向上などが見込めない状況で合成コマンドを定義するメリットはない. また, このような合成コマンドをさらに合成することも無しではないが, 保守性が犠牲にならないかなど十分な注意を持って判断されるべきだろう.

### 操作内容を端的に呼べるとき → する

コマンドの(合成)内容が固有名詞としての対応概念を持たなくても, その操作内容を(対応概念と関連のある形で)端的に呼べるのであれば, 広義の対応概念を持っているようなものだ. この場合はコマンド化する利益がある場合が多い. たとえば,

$$
\int_{\Omega} \left( \frac{\partial u_n}{\partial x_i} v_n + u_n \frac{\partial v_n}{\partial x_i} \right) \varphi
$$

という積分を次のようなコードで書いているとして,

```tex
\integral{\left( \derivative{u_n}{x_i} v_n + u_n \derivative{v_n}{x_i}\right) \varphi}[over={\Omega}]
```

このコードの可読性を上げることを考える. `\derivative` 周りの対称性を利用してなんとかする方法も有り得るが, 今は `(\derivative +...)` を別の関数 `\varphi` と掛け合わせた上で積分するという定型的な操作に注目し, 以下のように"別の関数と一緒に積分する"という操作を表現するコマンドを定義する.

```tex
\integralWith{f=\derivative{u_n}{x_i} v_n + u_n \derivative{v_n}{x_i}, with=\varphi}[over={\Omega}, bracket=true]
```

`with=` を導入して掛け算の切れ目を明らかにすることで, brackets `()` の挿入を `bracket=` オプションに任せられ, コードの意味が見えやすくなった.

このような操作的なコマンド引数は, key-value形式のような説明的な仕立てにするとよい. 読み上げ的なコマンド引数(順)と相性がよいからだ.

なお, この操作だけなら `\integral` に `with=` と `bracket=` オプションを足せば足りる. ここでは, 積分(や微分)コマンドは放っておくと機能が肥大化していくことを危惧して, 初めから独立させることを選んだ. どちらが正解かは分からない. おそらく書いている文書やユーザーの好みに依るだろう.

## 否定的基準

### 入力速度を上げたいだけのとき → しない

コマンド化せず, その内容を高速入力するスニペットを定義した方が良い.

ソースコード上に残す文字は, 入力完了以降の将来時点における情報的価値が求められる. 書き終わった瞬間的に価値を失うなら, それは負の遺産となる. そんなコマンドは存在しないに越したことはない. コードは書く時間よりも読まれる時間の方が長いので, 書く際の都合よりも読む際の都合を優先すべきだ.

### 対応概念が定まらない記号を含むとき → しない

典型例には, 意味を持たない `\tilde` や `\prime` がついているコードがこの類型に当てはまる.

たとえば, `\tilde{f}` に対応概念がない場合, これは単に `f` と比較するためにチルダを付けただけの `f` とは関連のない関数であることが多い. この記号を使うべき理由はなく, `f_1` でも `\prime{f}` でも `g` でも別に構わないはずだ.

そのため, 記号が変更される可能性は他と比べて高いし, 変更される箇所は気まぐれで法則性がないことが予想される. つまり, コマンドによる記号管理と相性が悪い. なぜなら, 同管理法は, 変更がある場合は一括変更であること前提としていて, 局所変更は想定していないからだ. この手の場当たり的記号をコマンド内に引き込むと保守性が著しく損なわれる. よって, 完全に締め出した方がよい.

対応概念が定まらない記号を含むコマンド合成は無論ご法度で, できれば省略コマンドにさえ含めない方がよい. `\xxAbbreviation{x}{\~f}` のようにコマンド定義には含めず引数の値として与える方が安全だ.

コマンドが対応概念を持つことが如何に重要かを示す好例ではないだろうか.

## 個別判断

### コマンドに付ける添字管理がしたいとき → ?

ここで言う「添字」は, 数字に当たるものだけでなく, 記号も含む. たとえば, $P_X$ の $X$ も添字として扱う.

上で, `\derivative` コマンドの引数として, `\derivative{f}{x}[order={1,2}, subscript={1,2}]` のような添字管理オプションは自然ではないと書いた. これは個別例であり, 添字管理するコマンドやオプション全般についてそう言っているわけではない.

その標準的表記法に添字が込められていると見なせる概念は多くある. その場合は, 対応概念のコマンドに添字管理用のオプションを与えることは自然だろう.

たとえばノルムや内積, 距離などの特定の空間に付随する計量関数は, 本来その空間を明示して `\norm{f}_X` などと書くべきだ. 文脈から明らかな場合は省略して単に `\norm{f}` と書く慣習があるに過ぎない. この省略内容を明示するオプションはむしろある方が自然だろう.

`\norm{f}_X` や `\norm{f}_1` のような書き方しかしないと決まっているならコマンド化の必要性は薄れるが, ノルム空間に対する Notation を `\NormSp{X}` のようなコマンドで管理している場合は添字管理をコマンドで行った方がよい. `\norm{f}_{\NormSp{X}}` は明らかに冗長だし, braces `{}` の連続も目に優しくない. 代わりに, `\norm{f}[space=X]` などと書けるようにするとよいだろう.

類似例として, 写像等が誘導・生成する概念はこのような添字管理方法が自然な場合が多い. たとえば, 包含写像 $i: W \to X$ が引き起こす相対位相 $\mathcal{O}_W$, 確率変数 $X$ が引き起こす確率測度($X$ の分布) $P_X$ などがそれに当たる.