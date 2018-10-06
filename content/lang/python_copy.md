---
title: "Python，numpyのCopyについて"
date: 2018-08-29T14:16:57+09:00
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

pyhtonのライブラリnumpyを使っていたとき普通に変数を代入するだけだと同じオブジェクトを指すようになってしまうので`numpy.copy()`というメソッドを使ってやって複製を作る必要がある．
```Python
import numpy as np

a = np.array([2, 1])
b = a
b[0] = 3
print(a) # => array([3, 1])
print(b) # => array([3, 1])
print(a is b) # => True
c = a.copy()
print(c) # => array([3, 1])
c[1] = 40
print(a) # => array([3, 1])
print(c) # => array([3, 40])
print(a is c) # => False
```

## References
- [numpy.copy — NumPy v1.15 Manual](https://docs.scipy.org/doc/numpy/reference/generated/numpy.copy.html)
- [copyモジュール 浅いコピーと深いコピー - Python学習講座](https://www.python.ambitious-engineer.com/archives/661)
