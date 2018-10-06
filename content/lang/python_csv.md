---
title: "Pythonでのcsvの扱い方"
date: 2018-09-16T18:50:25+09:00
description: ""
disable_comments: false # Optional, disable Disqus comments if true
authorbox: false # Optional, enable authorbox for specific post
toc: false # Optional, enable Table of Contents for specific post
mathjax: true # Optional, enable MathJax for specific post
draft: true
categories:
  - "lang"
tags:
  - "python"
---

pythonでのcsvの読み込みをいつも忘れてしまうのでメモ．

## csv libraryの場合
#### 読み込み
```Python
import csv

with open("file.csv", "r") as f:
    reader = csv.reader(f)
    header = next(reader) # headerを飛ばす場合(headerがない場合は最初の行が飛ばされるので注意)
    for row in reader:
      print(row)
```

#### 書き込み
```Python
import csv

with open("file.csv", "w") as f: # ファイルに追記したい場合は"w"の部分を"a"にする
    writer = csv.writer(f, lineterminator=",")
    header = next(reader) # headerを飛ばしたい場合(headerがない場合は最初の行が飛ばされるので注意)
    for row in reader:
      print(row)
```

## numpyの場合

## pandasの場合
#### 読み込み
```Python
import pandas as pd

df = pd.read_csv("file.csv")
```

#### 書き込み

## References
- [211.CSVファイルの読み込み・保存 · GitBook](http://docs.fabo.io/python/numpy/numpy012.html)
