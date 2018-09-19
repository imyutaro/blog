---
title: "PythonのDictionary型について"
date: 2018-08-29T14:11:31+09:00
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


indexを取得するにはfor文でループを回す．
```python
dic = {'key1': 110, 'key2': 270, 'key3': 350}

for i, value in enumerate(dic):
    print(i, value) # ループインデックスと値が出力

for i, (key, value) in enumerate(dic.items()):
    print(i, key, value) # ループインデックスとkeyと値が出力
```

## References
- [dictionary(辞書)型のループ処理 - Python学習講座](https://www.python.ambitious-engineer.com/archives/186)
