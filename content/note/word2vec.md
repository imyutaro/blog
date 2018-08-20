---
title: "Word2vecまとめ"
date: 2018-08-20T23:15:30+09:00
description: "about word2vec"
disable_comments: false # Optional, disable Disqus comments if true
authorbox: false # Optional, enable authorbox for specific post
toc: true # Optional, enable Table of Contents for specific post
mathjax: true # Optional, enable MathJax for specific post
draft: true
categories:
  - "note"
tags:
  - "NLP"
  - "機械学習"
---

word2vecは「周辺にある単語が似ている単語同士は意味も似ている」というハリスの分布仮説に基づき，ある単語から周辺単語を予測するSkip-gram(SG)モデルと周辺単語からある単語を予測するContinuous Bag of Words(CBOW)モデルの2種類あり，Skip-gramを中心に説明を行う．

## Skip-gramモデル
Skip-gramモデルは1つの単語に対して周辺の単語を予測するモデルで
最大化すべき目的関数(損失関数)は以下の式\eqref{eq:maximize_func}である．
\begin{align}
  \frac{1}{|V|} \sum\_{t=1}^{|V|} \sum\_{-c \le j \le c, j\neq 0} \log p(w\_{t+j}|w_t)
  \label{eq:maximize_func}
\end{align}
また式\eqref{eq:maximize_func}内の条件付き確率は式\eqref{eq:conditional_probability}で表される．
\begin{align}
  p(w_c|w_t)=\frac{\exp \left(v_t^{\mathrm{T}}\; v_c\right)}{\sum\_{k=1}^{|V|}\exp \left(v_t^{\mathrm{T}}\; v_k\right)}
  \label{eq:conditional_probability}
\end{align}
式\eqref{eq:maximize_func}，式\eqref{eq:conditional_probability}中の変数について書く．$c$はwindowサイズ．注目する単語から$c$個の周辺単語を学習の際に用いる．$w_t$は注目している単語，$w_c$は$w_t$の周辺単語．$v_t$は単語$w_t$を表す特徴ベクトル．つまり，$v_c$は周辺単語$w_c$を表す特徴ベクトル．$V$は全単語の集合．$|V|$はすべての単語数を表す．この変数の中の単語の特徴ベクトルを学習で求める．また，この特徴ベクトルのことを「分散表現」や「埋め込みベクトル(Embedding vector)」といったりする．

式\eqref{eq:maximize_func}は単語$w_t$があったとき単語$w\_{t+j}$が出現する確率を表している．式\eqref{eq:conditional_probability}の分母は全単語のベクトル$v_k$と注目単語のベクトル$v_t$の内積をeの肩に乗せたもの．分子は周辺単語のベクトル$v_c$と注目単語のベクトル$v_t$の内積をeの肩に乗せたもの．これは注目単語ベクトルに関しての

この計算は現実的ではないので階層的softmaxやnegative samplingによって学習する際の計算コストを削減する．
[](./img/word2vec_sikpgram.png)

具体的な順伝播の計算を以下に書く．1単語から1単語を予測する図のようなCBOWのモデルを簡単のため考える．
[](./img/word2vec_simpleCBOW.png)\
変数について説明する．$|V|$は全単語の数，$c$はwindowサイズ，$N$は中間層の次元．入力ベクトル$\boldsymbol{x}=\{x_1, x_2, \cdots, x\_{|V|}\}$はone-hotベクトル．one-hotベクトルとはある1つの$x_k$に対して1の値を取り，それ以外の$x\_{k'}$では0を取るようなベクトルである($x_k=1, x\_{k'}=0 \;\; \rm{for} \;\; k\neq k'$)．
入力層と中間層の間の重み行列は$|V|\times N$行列である．それぞれの行は$N$次元のベクトル$\boldsymbol{v}\_{w}$であり，$\boldsymbol{v}\_{w}$は入力層の単語$w$に対応したベクトルである．



## Negative Sampling
skip-gramモデルをベースとした学習の計算をより効率的に行う手法である．基本的な考え方は注目している単語と単語の集合からランダムにとってきた単語のうち，windowサイズの範囲内にある単語(注目している単語とよく共起する単語)については確率が高く，それ以外の単語については共起しない確率が高くなるような目的関数を設定しその関数を最大化するように学習する．\

注目する単語$w_t$が単語$w_c$と共起している確率(windowサイズ内に出現する確率)を$p(w_c|w_t)$と表すと，$w_c$が共起しない確率は$1-p(w_c|w_t)$と表せる．$p(w_c|w_t)$をsigmoid関数で表すと$p(w_c|w_t)$は以下のように表せる．
\begin{align}
  p(w_c|w_t) = \sigma(v_t^{\mathrm{T}} \; v_c) = \frac{1}{1+\exp (-v_t^{\mathrm{T}} \; v_c)} \nonumber
\end{align}

よって$w_c$が共起しない確率$1-p(w_c|w_t)$は以下のようになる．
\begin{align}
  1-p(w_c|w_t) = 1-\sigma(v_t^{\mathrm{T}} \; v_c) &= 1-\frac{1}{1+\exp (-v_t^{\mathrm{T}} \; v_c)} \nonumber \\\\\\
  &=\frac{1 + \exp (-v_t^{\mathrm{T}} \; v_c) -1}{1+\exp (-v_t^{\mathrm{T}} \; v_c)} \nonumber \\\\\\
  &=\frac{\exp (-v_t^{\mathrm{T}} \; v_c)}{1+\exp (-v_t^{\mathrm{T}} \; v_c)} \nonumber \\\\\\
  &=\frac{1}{\exp (v_t^{\mathrm{T}} \; v_c)+1} \nonumber \\\\\\
  &=\frac{1}{1+\exp (v_t^{\mathrm{T}} \; v_c)} \nonumber \\\\\\
  &\Bigl( =\sigma(-v_t^{\mathrm{T}} \; v_c) \Bigr) \nonumber
\end{align}

これらを用い，新たに以下の式\eqref{eq:new_maximize_func}を目的関数に設定する．
\begin{align}
  &-\log p(w_c|w_t)\prod\_{w_r \, \in V\_{neg}}(1-p(w_r|w_t)) \nonumber\\\\\\
  \Leftrightarrow &-\log \sigma(v_t^{\mathrm{T}} \; v_c)\prod\_{w_r \, \in V\_{neg}}(1-\sigma(v_t^{\mathrm{T}} \; v_r)) \nonumber\\\\\\
  \Leftrightarrow &-\left(\log \sigma(v_t^{\mathrm{T}} \; v_c) + \log \prod\_{w_r \, \in V\_{neg}}(1-\sigma(v_t^{\mathrm{T}} \; v_r)) \right) \nonumber\\\\\\
  \Leftrightarrow &-\left(\log \sigma(v_t^{\mathrm{T}} \; v_c) + \sum\_{w_r \, \in V\_{neg}}\log (1-\sigma(v_t^{\mathrm{T}} \; v_r)) \right) \nonumber\\\\\\
  \Leftrightarrow &-\left(\log \sigma(v_t^{\mathrm{T}} \; v_c) + \sum\_{w_r \, \in V\_{neg}}\log \sigma(-v_t^{\mathrm{T}} \; v_r) \right) \nonumber\\\\\\
  \Leftrightarrow &-\log \sigma(v_t^{\mathrm{T}} \; v_c) - \sum\_{w_r \, \in V\_{neg}}\log \sigma(-v_t^{\mathrm{T}} \; v_r)
  \label{eq:new_maximize_func}
\end{align}

$V\_{neg}$は注目する単語と共起しない単語の集合．つまりwindowサイズの外から選ばれた単語．この目的関数を最大化するということは，注目する単語$w_t$と共起する単語$w_c$についての確率は大きくし，注目する単語と共起しない単語$w_r$に対しての確率は小さくするような操作である．


## 参考文献
- [The backpropagation algorithm for Word2Vec | Marginalia](http://www.claudiobellei.com/2018/01/06/backprop-word2vec/)\
- [A Word2Vec Keras tutorial - Adventures in Machine Learning](http://adventuresinmachinelearning.com/word2vec-keras-tutorial/)\
- [David Meyer, How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)\
- [word2vecのソースを読んでみた](https://qiita.com/mash0510/items/347964f3eb2e080ea7a4#%EF%BC%92%EF%BC%93-%E9%AB%98%E9%80%9F%E5%8C%96%E3%81%AE%E6%89%8B%E6%B3%95negative-sampling)\
- [Word2Vec のニューラルネットワーク学習過程を理解する](http://tkengo.github.io/blog/2016/05/09/understand-how-to-learn-word2vec/)\
- [Distributed Representations of Words and Phrases and their Compositionality](http://arxiv.org/abs/1402.3722)\
- [Yoav Goldberg and Omer Levy, word2vec Explained: deriving Mikolov et al.'s negative-sampling word-embedding method](http://arxiv.org/abs/1402.3722)\
