---
title: "Conclusion"
---

## 他流儀との比較

以上で紹介したのは, よく現れるコードではなく, よく使う概念や概念操作を基点にコマンドを組み立てる流儀である.

前者の大らかな流儀(?)に比べると, 後者の難易度はおそらく高い. 良いコマンドを抽出するためには, 適切な対応概念や概念操作を見抜かなければならない. それには当該分野の理解が欠かせない. (ポジティブな言い方をすれば当該分野の知識をコーディングに活かせる流儀である.) 実行難易度は客観的というより主観的に決まる.

多少の難しさの代わりに, コードの可読性・保守性は高いものに仕上がりやすい. また, 文脈依存性が極めて低いため, 移植性に優れる. これはコード資産が積み上がりやすいことを意味する. 導入が早いとその収益期間を長く取れてお得である. $\LaTeX$生態系の息が途絶えないという前提は付くが(重要).

## 制約との付き合い方

実践してみると分かるが, 「対応概念に照らして自然であるか」というコマンド設計方針は, かなり強い制約である. 同時に, この制約は上述のメリットの源泉でもある. 最早この制約こそがこの流儀の正体だと言っても過言ではない. しかし, この制約に100%平服する必要はない. それでは窮屈過ぎるだろう. どの程度忠実に守るかは, ユーザーの好みに応じて調整できるし, おそらくそうした方がよい.

ただし, 窮屈が生じているのは, 目的の機能を自然に導入できる対応概念・概念操作をユーザーが見抜けていないだけ, という状況は過去の自分を思い返すと非常によくある. 単に私の頭の問題という可能性は否定できないものの, この流儀を取り入れるなら一度は厳格に運用してみることを老婆心ながら勧めたい.

いずれにせよ, コードの改善を動機として, 都合の良い概念の発見を試みる機会は多かれ少なかれ自然と増えるだろう. この行為に時間を取られることもある. しかし, 文書コンテンツと関連の薄いコードをad-hocに弄ったり修正したりする経験に比べれば, 多くの書き手にとって望ましい時間の使い方なのではないだろうか. そこでの発見はコンテンツに対する理解や見る目を豊かにするものであることが多いはずだし, その結果として手に入ったコードは他の文書でも活躍してくれるのだから.