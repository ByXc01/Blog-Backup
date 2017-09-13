---
title: _Noreturn
date: 2017-09-06 17:01:08
tags: _Noreturn
---

# 屁大點事寫博客
今天我學了 `_Noreturn` 這個函數說明符，書上的內容不多就兩段字：
![書上的](https://raw.githubusercontent.com/ByXc01/Blog-image/master/_Noreturn/_Noreturn2.png "_Noreturn")

<!--more-->

 準備寫個小程序測試一下。 結果還是出了 **bug**。

代碼如下:
``` c
	#include <stdio.h>

	_Noreturn void hello(double);
	int main(void)
	{
    	hello(66.66);
    	printf("empty\n");
	
    	return 0;
	}

	_Noreturn void hello(double n)
	{
    	printf("n = %f\n", n);
	}
```

我測試到的結果：
![no_return1](https://raw.githubusercontent.com/ByXc01/Blog-image/master/_Noreturn/no_return1.png "bug")

有兩個問題：
第一個， 我沒有用 `return;` 卻有警告: 'noreturn' 函數返回了。
第二個， 輸出的結果也出乎我意料， 輸出了兩次 `n` 的值。

俗話說：“有問題，問度娘”。 我搜了一下 `_Noreturn` (C11)關鍵字，結果沒有找到。 後來去查了 [C語言參考手冊](http://zh.cppreference.com/w/c/language/_Noreturn "cppreference") 才知道問題出在哪。

我截圖了：
![cppreference](https://raw.githubusercontent.com/ByXc01/Blog-image/master/_Noreturn/_Noreturn1.png "_Noreturn")
解釋部分已經說的很清楚了。**`_Noreturn` 關鍵字出現于函數聲明中，并指定函數不會通過 `return` 語句或抵達函數體結尾而結束。若聲明了 `_Noreturn` 的函數返回了， 則行為未定義。**

* * *

可能我這種情況也是未定義(抵達函數結尾而結束)。
以下是按照**C語言參考手冊**解釋的要求更改了代碼：
``` c
	#include <stdio.h>
	#include <stdlib.h>     // for exit()
	
	_Noreturn void hello(double);
	int main(void)
	{
		hello(66.66);
		printf("empty\n");
	
		return 0;
	}
	
	// 解釋中說到 _Noreturn 關鍵字出現在函數聲明中。
	// _Noreturn 在函數定義好像可有可無(本小白也不敢確定)， 去掉也完美編譯運行。
	_Noreturn void hello(double n)
	{
    	printf("n = %f\n", n);
    	exit(0);
	}
```
結果是我想要的， `gcc`不報警， 輸出也正常了。
![no_return2](https://raw.githubusercontent.com/ByXc01/Blog-image/master/_Noreturn/no_return2.png "ok")

## 結束語
最后說一下： 多敲代碼， 不懂得地方多用搜索引擎， 實在找不到再去問人或發貼子。
