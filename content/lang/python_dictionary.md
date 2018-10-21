---
title: "PythonのDictionary型について"
date: 2018-08-29T14:11:31+09:00
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


indexを取得するにはfor文でループを回す．
```python
dic = {'key1': 110, 'key2': 270, 'key3': 350}

for i, key in enumerate(dic.keys()):
    print(i, key) # enumerateの値と辞書のkeyが出力

# dictをforで回すとkeyが取得できる．
for i, key in enumerate(dic):
    print(i, key) # enumerateの値と辞書のkeyが出力

for i, value in enumerate(dic.values()):
    print(i, value) # enumerateの値と辞書の値が出力

for i, (key, value) in enumerate(dic.items()):
    print(i, key, value) # enumerateの値と辞書のkeyと値が出力
```

keyや値をインデックスで指定したいときはlistになおす．
```python
dic = {'key1': 110, 'key2': 270, 'key3': 350}

d_key_list = list(dic.keys())
# => ['key1', 'key2', 'key3']
d_key_list[0] #=> 'key1'

d_val_list = list(dic.values())
# => [110, 270, 350]
d_val_list[0] #=> 110
```

## References
- [dictionary(辞書)型のループ処理 - Python学習講座](https://www.python.ambitious-engineer.com/archives/186)
- [Pythonの辞書（dict）のforループ処理(keys, values, items)|note.nkmk.me](https://note.nkmk.me/python-dict-keys-values-items/)
