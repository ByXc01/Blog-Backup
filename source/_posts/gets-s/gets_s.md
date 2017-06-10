---
title: C11 gets_s() 函數
date: 2017-05-29 18:20:27
tags: gets_s
---

### C11 標準 (IS0/IEC 9899.201x) 中， gets() 函數被刪除， 引進了新的函數 gets_s() 。

C11 K.3.5.7.1 The gets_s function 代碼如下:

```c
	#define __STDC_WANT_LIB_EXT1__ 1
	#include <stdio.h>
	char * gets_s(char *s, rsize_t n);
```
<!--more-->

### 因為目前 GCC 中還沒有完全實現該標準， 因此 gets_s() 函數尚未包含在目前的 GNU 工具鏈中。 Clang 也暫時沒有增加對 gets_s() 的支持。

在 《*c primer plus*》 也沒過多的介紹。

![get_s() 介紹](http://opkl2tvjd.bkt.clouddn.com/gets_s.png "gets_s()介紹")

詳細可查看[C 語言參考手冊](http://zh.cppreference.com/w/c/io/gets "C 語言查考手冊")
