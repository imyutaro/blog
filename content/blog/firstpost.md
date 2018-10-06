---
title: "Hugo+GitHub Pagesでblog作成"
date: 2018-08-10T21:16:24+09:00
description: ""
disable_comments: false # Optional, disable Disqus comments if true
authorbox: false # Optional, enable authorbox for specific post
toc: true # Optional, enable Table of Contents for specific post
mathjax: true # Optional, enable MathJax for specific post
draft: false
categories:
  - "blog"
tags:
  - "others"
---

## Blogをはじめた
自分が勉強していることをoutputとしてblogにまとめることにした．このblogは[Hugo](https://gohugo.io/)というGoで作られた静的サイトジェネレータを使って作成した．


## Hugoについて
HugoはGo製で速いらしい．最初，[Jekyll](https://jekyllrb.com/)というRuby製の静的サイトジェネレータを利用しようと思ったが記事数が多くなると重くなるというのを見てHugoにした．また，HugoもJekyllも記事をmarkdownで書けるのでとても楽．数式も設定すれば書けるので便利．

Giraffe AcademyというYouTubeチャンネルの解説がわかりやすくそれを参考に作成した．

<iframe width="100%" height="385" src="https://www.youtube.com/embed/qtIqKaDlqXo?list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen frameborder="0"></iframe>

[Giraffe AcademyのHugo解説 playlist](https://www.youtube.com/watch?v=qtIqKaDlqXo&list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3)

テーマは[mainroad](https://themes.gohugo.io/mainroad/)を利用している．

### 数式を表示
また，数式を書くために以下のサイトを参考に数式表示できるようにした．

- [Hugoで数式を導入します | しさく](http://iimuz.github.io/post/2016/11/hugotex/)

`layout/partials/mathjax_support.html`というファイルを作って以下のコードを書いて数式をレンダリングできるようにした．

```html:mathjax_support.html
<!-- tex -->
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
      tex2jax: { inlineMath: [['$','$'], ["\\(","\\)"]] }
  });
</script>
<script
    type="text/javascript"
    src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" />
</script>
```
数式表示は例えばこんな感じ
$$
\sum_{i=0}^N a_i
$$

上記の設定だと`\sum`が2回続くつまり，`\sum_{a=0}^A \sum_{b=0}^B`としたりしたらうまく表示できない．これは，`\prod`や `\int`などでも起こる． \
`\sum_{}`の`_`(アンダースコア)の前に`\`(バックスラッシュ又は`¥`)を入れれば表示される．

- [Setting MathJax with Hugo | Hi, I am David](https://divadnojnarg.github.io/blog/mathjax/)
- [Supported Content Formats | Hugo](https://gohugo.io/content-management/formats/#solution)

また，ベクトルのための太字は`\boldsymbol{}`を使う．

- $v_i, a_i, x_i$(通常)
- $\boldsymbol{v}_i, \boldsymbol{a}_i, \boldsymbol{x}_i$  (太字)

Atomでmarkdownのプレビューが出来るようにと思って上記の設定をしたが結局`_`を入れないといけないならKaTeXを使ったほうが良いかも?\
KaTeXならshortcodeを使ったり数式があるときだけjsファイルを読み込んだりするから良いかもしれない，localhostでプレビューする必要があるけど．．．

### 式番号の表示
式番号を自動で入れる場合には以下を`layouts/partials/mathjax_support.html`に書く．
```html
MathJax.Hub.Config({
  // Autonumbering by mathjax
  TeX: { equationNumbers: { autoNumber: "AMS" } }
});
```

そして`layouts/partials/footer.html`に以下を書く．
```html
{{- partial "mathjax_support.html" . -}}
```

書いたあとの`layouts/partials/mathjax_support.html`．
```html:mathjax_support.html
{{ if and .IsPage (eq (.Param "mathjax") true) }}
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [['$$','$$'], ['\[','\]']],
    processEscapes: true,
    processEnvironments: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre','code'],
    TeX: { equationNumbers: { autoNumber: "AMS" },
         extensions: ["AMSmath.js", "AMSsymbols.js"] }
  }
});

MathJax.Hub.Config({
  // Autonumbering by mathjax
  TeX: { equationNumbers: { autoNumber: "AMS" } }
});
</script>
{{ end }}
```

書いたあとの`layouts/partials/footer.html`．
```html:footer.html
</div><!-- .wrapper -->
  <footer class="footer">
    <div class="container container-inner">
      <div class="footer__copyright">&copy; {{ now.Format "2006" }} {{ .Site.Title }}. <span class="footer__copyright-credits">{{ T "footer_credits" | safeHTML }}</span></div>
    </div>
  </footer>
</div><!-- .container-outer -->

<script>
var navigation = responsiveNav(".menu", {
  navClass: "menu--collapse",
});
</script>
{{- partial "mathjax.html" . -}}
{{- partial "mathjax_support.html" . -}}
</body>
</html>
```

通常の`$$`で囲んだ数式には式番号は表示されない．LaTeXの書き方で`equation`か`align`で囲んだ数式にのみ式番号が表示される．
\begin{align}
  p(w_c|w_t)=\frac{\exp \left(v_t^{\mathrm{T}}\; v_c\right)}{\sum\_{k=1}^{|V|}\exp \left(v_t^{\mathrm{T}}\; v_k\right)}
\end{align}
式番号を表示させたくないところには`\nonumber`をつける．基本的にはLaTeXの数式の書き方だが，改行は`\`を6つ並べないといけない．

### Syntax highlightの設定
syntax highlightについては以下の記事を参考に行った．

- [Syntax Highlighting | Hugo](https://gohugo.io/content-management/syntax-highlighting/)
- [HugoでのシンタックスハイライトにPython Pygmentsが不要となった - SIS Lab](https://www.meganii.com/blog/2017/10/14/hugo-syntax-highlighting/)

syntax highlightを有効にするには`config.toml`に以下を書き加える．
```toml
pygmentsCodefences = true
pygmentsStyle = "themename" #themenameのところにthemeの名前を入れる
```
自分で色を設定したい人は`config.toml`に
```toml
pygmentsUseClasses = true
```
を加え，自分でcssコードを書く．または，既存のテーマの色を自分で少し設定し直す．既存のテーマのcssコード得るには以下のコードを実行する．
```bash
hugo gen chromastyles --style=themename > tmp.css
# themenameのところをテーマの名前にする．
```
得られたcssコードを自分の`static/css/style.css`に書き加える．

```css
/* Background */ .chroma { background-color: #f0f0f0 }
/* Error */ .chroma .err {  }
/* LineTableTD */ .chroma .lntd { vertical-align: top; padding: 0; margin: 0; border: 0; }
/* LineTable */ .chroma .lntable { border-spacing: 0; padding: 0; margin: 0; border: 0; width: auto; overflow: auto; display: block; }
/* LineHighlight */ .chroma .hl { display: block; width: 100%;background-color: #ffffcc }
/* LineNumbersTable */ .chroma .lnt { margin-right: 0.4em; padding: 0 0.4em 0 0.4em; }
/* LineNumbers */ .chroma .ln { margin-right: 0.4em; padding: 0 0.4em 0 0.4em; }
/* Keyword */ .chroma .k { color: #007020; font-weight: bold }
/* KeywordConstant */ .chroma .kc { color: #007020; font-weight: bold }
/* KeywordDeclaration */ .chroma .kd { color: #007020; font-weight: bold }
/* KeywordNamespace */ .chroma .kn { color: #007020; font-weight: bold }
/* KeywordPseudo */ .chroma .kp { color: #007020 }
/* KeywordReserved */ .chroma .kr { color: #007020; font-weight: bold }
/* KeywordType */ .chroma .kt { color: #902000 }
/* NameAttribute */ .chroma .na { color: #4070a0 }
/* NameBuiltin */ .chroma .nb { color: #007020 }
/* NameClass */ .chroma .nc { color: #0e84b5; font-weight: bold }
/* NameConstant */ .chroma .no { color: #60add5 }
/* NameDecorator */ .chroma .nd { color: #555555; font-weight: bold }
/* NameEntity */ .chroma .ni { color: #d55537; font-weight: bold }
/* NameException */ .chroma .ne { color: #007020 }
/* NameFunction */ .chroma .nf { color: #06287e }
/* NameLabel */ .chroma .nl { color: #002070; font-weight: bold }
/* NameNamespace */ .chroma .nn { color: #0e84b5; font-weight: bold }
/* NameTag */ .chroma .nt { color: #062873; font-weight: bold }
/* NameVariable */ .chroma .nv { color: #bb60d5 }
/* LiteralString */ .chroma .s { color: #4070a0 }
/* LiteralStringAffix */ .chroma .sa { color: #4070a0 }
/* LiteralStringBacktick */ .chroma .sb { color: #4070a0 }
/* LiteralStringChar */ .chroma .sc { color: #4070a0 }
/* LiteralStringDelimiter */ .chroma .dl { color: #4070a0 }
/* LiteralStringDoc */ .chroma .sd { color: #4070a0; font-style: italic }
/* LiteralStringDouble */ .chroma .s2 { color: #4070a0 }
/* LiteralStringEscape */ .chroma .se { color: #4070a0; font-weight: bold }
/* LiteralStringHeredoc */ .chroma .sh { color: #4070a0 }
/* LiteralStringInterpol */ .chroma .si { color: #70a0d0; font-style: italic }
/* LiteralStringOther */ .chroma .sx { color: #c65d09 }
/* LiteralStringRegex */ .chroma .sr { color: #235388 }
/* LiteralStringSingle */ .chroma .s1 { color: #4070a0 }
/* LiteralStringSymbol */ .chroma .ss { color: #517918 }
/* LiteralNumber */ .chroma .m { color: #40a070 }
/* LiteralNumberBin */ .chroma .mb { color: #40a070 }
/* LiteralNumberFloat */ .chroma .mf { color: #40a070 }
/* LiteralNumberHex */ .chroma .mh { color: #40a070 }
/* LiteralNumberInteger */ .chroma .mi { color: #40a070 }
/* LiteralNumberIntegerLong */ .chroma .il { color: #40a070 }
/* LiteralNumberOct */ .chroma .mo { color: #40a070 }
/* Operator */ .chroma .o { color: #666666 }
/* OperatorWord */ .chroma .ow { color: #007020; font-weight: bold }
/* Comment */ .chroma .c { color: #60a0b0; font-style: italic }
/* CommentHashbang */ .chroma .ch { color: #60a0b0; font-style: italic }
/* CommentMultiline */ .chroma .cm { color: #60a0b0; font-style: italic }
/* CommentSingle */ .chroma .c1 { color: #60a0b0; font-style: italic }
/* CommentSpecial */ .chroma .cs { color: #60a0b0; background-color: #fff0f0 }
/* CommentPreproc */ .chroma .cp { color: #007020 }
/* CommentPreprocFile */ .chroma .cpf { color: #007020 }
/* GenericDeleted */ .chroma .gd { color: #a00000 }
/* GenericEmph */ .chroma .ge { font-style: italic }
/* GenericError */ .chroma .gr { color: #ff0000 }
/* GenericHeading */ .chroma .gh { color: #000080; font-weight: bold }
/* GenericInserted */ .chroma .gi { color: #00a000 }
/* GenericOutput */ .chroma .go { color: #888888 }
/* GenericPrompt */ .chroma .gp { color: #c65d09; font-weight: bold }
/* GenericStrong */ .chroma .gs { font-weight: bold }
/* GenericSubheading */ .chroma .gu { color: #800080; font-weight: bold }
/* GenericTraceback */ .chroma .gt { color: #0044dd }
/* TextWhitespace */ .chroma .w { color: #bbbbbb }
```

<!--
コードブロックにスライドバーを表示させたかった．．．
`style.css`のpre のところでmax-heightを決め，overflowをauto又はscrollにしたら縦方向はできたが，横方向は`white-space = pre`にしてmax-widthを決める必要があったがブラウザの大きさによってコードブロックの横幅が変わるのを考慮する方法がわからなかった．．．
ちなみにoverflowはx,yで横方向，縦方向の挙動を変えられる．css overflowで調べると挙動の種類がわかる．
-->

### Reading timeについて
Reading timeを`layouts/partials/post_meta.html`の最後に付け加えた．
```html
{{ if not .Site.Params.disableReadingTime }}  　~ {{ .ReadingTime }} minute read ~{{ end }}
```

## GitHub Pagesの設定
Hugo公式のページ([Host on GitHub | Hugo](https://gohugo.io/hosting-and-deployment/hosting-on-github/))を参考にGitHub pagesの設定を行った．\
GitHub Pagesには2種類のサイトの形式がある．サイトの形式によって与えられるURL，レンダリングされる場所が異なる．対応は以下のようになっている．

| Type | URL | レンダリング場所 |
| :---: | :---: | :---: |
| User/Organization Pages | https://\<USERNAME/ORGANIZATION\>.github.io/ | レポジトリ直下 |
| Project Pages | https://\<USERNAME/ORGANIZATION\>.github.io/\<PROJECT\>/ | docsフォルダ直下 |

\<USERNAME/ORGANIZATION\>にGitHubのアカウント名が入り，\<PROJECT\>には作成したリポジトリ名が入る．\
生成元のファイルなどもgit管理したいのでProject Pagesの設定を行った．手順は以下の通り．

1. GitHubでリポジトリを作成．（例としてリポジトリ名をblogとする．）
2. `config.toml`に以下を追加する．
    

3. hugoで作成したディレクトリで`hugo`, `git init`, `git add .`, `git commit -m "first commit"`をする．
4. GitHubにpush．
5. GitHubのリポジトリのところにある`Settings>GitHub Pages>Source`をmaster branch /docs folderにする．

これで3.の`hugo`で生成されたdocs folder以下の静的サイトが公開される．

ひとまずこれで公開する．Google analyticsやgoogle search console，Disqus(コメント)の導入，サイト内検索，カスタムドメインはまた今度．．．

<!--
## Google analytics \
## google search console
## Disqusの導入
-->

## References
Hugoについて
: [Giraffe AcademyのHugo解説 playlist](https://www.youtube.com/watch?v=qtIqKaDlqXo&list=PLLAZ4kZ9dFpOnyRlyS-liKL5ReHDcj4G3)

数式の表示
: [Hugoで数式を導入します | しさく](http://iimuz.github.io/post/2016/11/hugotex/)
: [Doesn't parse when there are more than one \sum or \inf in an equation · Issue #984 · mathjax/MathJax](https://github.com/mathjax/MathJax/issues/984)
: [Setting MathJax with Hugo | Hi, I am David](https://divadnojnarg.github.io/blog/mathjax/)
: [Using MathJax With Hugo Mainroad](https://pennbay.github.io/tech/mathjax.with.hugo/)

Syntax highlight
: [Syntax Highlighting | Hugo](https://gohugo.io/content-management/syntax-highlighting/)
: [HugoでのシンタックスハイライトにPython Pygmentsが不要となった - SIS Lab](https://www.meganii.com/blog/2017/10/14/hugo-syntax-highlighting/)

GitHub Pages
: [Host on GitHub | Hugo](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

その他
: [Hugoでブログ作成](https://tkat0.github.io/blog/writing-blog-with-hugo-1/)

<!--
listのネストについてhtmlに変換したら何故か<p>タグが入ってしまう問題
[同じissue](https://github.com/russross/blackfriday/issues/235)

サイト内全文検索
https://snap.textgh.org/201801152012/

ソーシャルボタンについて
https://aakira.app/blog/2018/08/share/
-->
