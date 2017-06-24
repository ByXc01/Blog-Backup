---
title: 字符串和空字符
date: 2017-05-29 17:52:12
tags: 空字符
---

## 提起空字符大家應該都不陌生，在 c 語言中， 沒有專門的字符串變量， 沒有 string 類型， 通常就用一個字符數組來存放一個字符串。

<!--more-->

然而為什麼今天要專門寫一篇博客呢？ 原因是前不久第一次遇到一個關於空字符 `\0` 的 bug 。

``` c
	/* File: reverse.c
	 * Author: ByXc
	 * About: reverse
	 * Version: 1.0
	 * Compiler: gcc 5.1.0 for MinGw
	 * Date: 20170513
	 * Github: ByXc01
	 * Blog: http://ByXc01.github.io
	 */

	#include <stdio.h>
	#include <string.h>

	int main(void)
	{
		int i_count;
		char ch_string[40];

		printf("Please enter a string:");
		scanf("%s", ch_string);
	
		for (i_count = strlen(ch_string); i_count >= 0; i_count--)
		    printf("%c", ch_string[i_count - 1]);        //i_count - 1, 是因为字符串后有一個空字符 \0 (ASCII = 0)
		printf("\nThe all! ");

		return 0;
	}
	
    /* 關於空字符的問題：在minGW,codeblocks, visual studio 2015。輸出的是一個像空格一樣的字符，
     * 在手機c4droid, cide, aide, c4droid toolchain 的gcc5.2.0輸出空字符都是不可見得。。
     * 其他其他編譯器就不清楚了。。  so， 看情況是否使用i_count - 1
     */

```

這個程序很簡單， 是 《*c primer plus*》 第六章的一道編程練習題， 輸入一個字符串， 程序將字符串倒序輸出。 剛開始我沒有用 `i_count - 1`， 覺得有點奇怪，怎麼倒序輸出的字符串有一個 **“空格”**， 輸出如下：

![空字符](http://opkl2tvjd.bkt.clouddn.com/null_string.png "空字符")

**後來測試了幾個平台， 在 windows 環境下是會輸出一個類似格的字符， 為什麼說是類似呢？ `if (*(ch_string + 4) == ' ')` 表達式為假。 在 Linux 環境下則輸出為不可見得（Windows 上用 msym2 輸出也是不可見的):**

![空字符](http://opkl2tvjd.bkt.clouddn.com/null_string2.png "空字符")

因為折騰的蠻久的， 印象也特別深刻。

## 接著在第十一章又見空字符

```c
	char cha_string1[] = { 'B', 'y', 'X', 'c', '\0' };
	char cha_string2[] = { "ByXc" };
	char * chp_string3 = { "ByXc" };
	char * chp_string4 = "ByXc";
	char cha_string3[5];

	cha_string[0] = 'B', 
	cha_string[1] = 'y', 
	cha_string[2] = 'X', 
	cha_string[3] = 'c';
```

以上的都是一個字符串， 在指定數組大小時， 要確保數組的元素個數要比字符串長度少 1 （為了容納空字符）。

**所有未被使用的元素都會自動初始化為 0 （這裡的 0 指的是 char 形式的空字符， 不是數字字符 0)**

![空字符](http://opkl2tvjd.bkt.clouddn.com/null_strin3.png "空字符")

讓我感覺想不通的還是程序清單 11.13 的輸出:

```c
	/* nono.c -- 千萬不要模仿 */
	#include <stdio.h>
	int main(void)
	{
		char side_a[] = "Side A";
		char dont[] = { 'W', 'O', 'W', '!' };
		char side_b[] = "Side B";
		puts(dont);		// dont 不是一個字符串， 應是一個字符數組

		return 0;
	}
```

由於 dont 缺少一個表示結束的空語句， 所以它不是一個字符串， 因此 puts() 不知道在何處停止。 它一直打印 dont 後面內存的內容， 知道發現一個空字符為止。 為了讓 puts() 能盡快能讀到空字符， 我們把 dont 放在 side_a 和 side_b 之間。 下面是該程序的一個運行示例：

```c
	WOW!Side A
```

**我們使用的編譯器吧 side_a 數組存儲在 dont 數組之後， 所以 puts() 一直輸出至遇到 side_a 中的空字符。 你所用的編譯器輸出的內容可能不同， 這取決於編譯器如何在內存中存儲數據。 如果刪除程序的 side_a 和 side_b 數組會怎樣？ 通常內存中有許多空字符， 如果幸運的話， puts() 很快就會發現一個。 但是， 這樣做很不靠譜。**

***

然而我的輸出為：

```
	WOW!
```

然後我又在 armv7l（gcc 6.1.0), aarch64(gcc 6.1.0), x64(gcc 6.3.0) ubuntu(gcc 6.3.0)執行了一遍。結果都是 WOW! 無論是 puts() 還是 printf() 都是一樣。 用 `gcc -S` 編譯出來的彙編我也看了一下， 似乎編譯器也沒自動添加一個空字符啊。

![沒有自動添加空字符啊](http://opkl2tvjd.bkt.clouddn.com/character_array.png "沒有自動添加空字符啊")

可能真是有關于編譯器吧， 我用 ubuntu(clang 3.8.0) 輸出跟書上一樣:

![clang](http://opkl2tvjd.bkt.clouddn.com/null_string4.png "clang")

用 `clang -S` 編譯出來的彙編如下：

``` 
		.text
		.file	"nono.c"
		.globl	main
		.align	16, 0x90
		.type	main,@function
	main:                                   # @main
		.cfi_startproc
	# BB#0:
		pushq	%rbp
	.Ltmp0:
		.cfi_def_cfa_offset 16
	.Ltmp1:
		.cfi_offset %rbp, -16
		movq	%rsp, %rbp
	.Ltmp2:
		.cfi_def_cfa_register %rbp
		subq	$32, %rsp
		leaq	-15(%rbp), %rdi
		movl	$0, -4(%rbp)
		movl	.Lmain.side_a, %eax
		movl	%eax, -11(%rbp)
		movw	.Lmain.side_a+4, %cx
		movw	%cx, -7(%rbp)
		movb	.Lmain.side_a+6, %dl
		movb	%dl, -5(%rbp)
		movl	.Lmain.dont, %eax
		movl	%eax, -15(%rbp)
		movl	.Lmain.sidw_b, %eax
		movl	%eax, -22(%rbp)
		movw	.Lmain.sidw_b+4, %cx
		movw	%cx, -18(%rbp)
		movb	.Lmain.sidw_b+6, %dl
		movb	%dl, -16(%rbp)
		callq	puts
		xorl	%esi, %esi
		movl	%eax, -28(%rbp)         # 4-byte Spill
		movl	%esi, %eax
		addq	$32, %rsp
		popq	%rbp
		retq
    .Lfunc_end0:
		.size	main, .Lfunc_end0-main
		.cfi_endproc

        .type   .Lmian.side_a,@object   # @main.side_a
		.section	    .rodata.str1.1,"aMS",@progbits,1

	.Lmain.side_a:
		.asciz	"Side A"
		.size	.Lmain.side_a, 7

		.type	.Lmain.dont,@object     # @main.dont
		.section	.rodata.cst4,"aM",@progbits,4
	.Lmain.dont:
		.ascii	"WOW!"
		.size	.Lmain.dont, 4

		.type	.Lmain.sidw_b,@object   # @main.sidw_b
		.section	.rodata.str1.1,"aMS",@progbits,1
	.Lmain.sidw_b:
		.asciz	"Sdie B"
		.size	.Lmain.sidw_b, 7


		.ident	"clang version 3.8.0-2ubuntu4 (tags/RELEASE_380/final)"
		.section	".note.GNU-stack","",@progbits
```
同樣也的， 好像也沒有自動加空字符。 雖然我也看不懂彙編。 但很明顯的是這一句：

```
	.Lmain.dont:
		.ascii	"WOW!"
		.size	.Lmain.dont, 4
```

size 只有 4， 可以說明沒有加空字符。 其它兩個都加了， 所以 size 才是 7。
在 c 代碼總也可用 `sizeof` 來檢測是否有空字符:

```c
	printf("side_a: sizeof = %zd, strlen = %zd \n", sizeof （side_a), strlen(side_a));
	printf("dont: sizeof = %zd, strlen = %zd \n", sizeof (dont), strlen(dont));
	// 因為 sizeof 會將空字符也納入計算， 而 strlen() 則會忽略空字符
```

反正知道字符數組和字符串的區別就好了。 太底層我也搞不懂， 因為也沒學過彙編和 gdb 。
