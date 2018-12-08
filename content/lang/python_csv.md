---
title: "Pythonでのcsvの扱い方"
date: 2018-09-16T18:50:25+09:00
description: ""
disable_comments: false # Optional, disable Disqus comments if true
authorbox: false # Optional, enable authorbox for specific post
toc: false # Optional, enable Table of Contents for specific post
mathjax: true # Optional, enable MathJax for specific post
draft: false
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
#### 読み込み
```python
import numpy as np

# csvファイルの場合
data = np.loadtxt("file.csv", delimiter=",")

# tsvファイルの場合
data = np.loadtxt("file.tsv", delimiter="\t")
```

#### 書き込み
```python
import numpy as np

# csvファイルの保存
np.savetxt("file.csv", x, delimiter=',')   # x is an array
```


## pandasの場合
#### 読み込み
```Python
import pandas as pd

df = pd.read_csv("file.csv")
```

#### 書き込み
```Python
import pandas as pd

df.to_csv("file.csv") # df is a DataFrame
```

## References
- [211.CSVファイルの読み込み・保存 · GitBook](http://docs.fabo.io/python/numpy/numpy012.html)
- [numpy.loadtxt — NumPy v1.15 Manual](https://docs.scipy.org/doc/numpy-1.15.0/reference/generated/numpy.loadtxt.html)
- [numpy.savetxt — NumPy v1.15 Manual](https://docs.scipy.org/doc/numpy-1.15.0/reference/generated/numpy.savetxt.html)
- [PythonでCSVの読み書き - Qiita](https://qiita.com/okadate/items/c36f4eb9506b358fb608)
- [pandasでよく使う文法まとめ - Qiita](https://qiita.com/okadate/items/7b9620a5e64b4e906c42)
