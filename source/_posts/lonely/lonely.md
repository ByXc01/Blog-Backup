---
title: lonely
date: 2017-07-14 20:23:24
tags: 隨筆
---

## bug ##
最近一直在看**文件輸入/輸出**，對**流**和**緩衝區**還不是很理解。然後去網上找`setvbuf()`函數的詳細的用法，無意中看到一段代碼，如下:

``` c
    /* test1.c */
    #include <stdio.h>
    
    char outbuf[512];               // 全局變量
    int main(void)
    {
        setbuf(stdout, bufout);     // 把緩衝區與輸出流相連
        
        puts("This is a test of buffer output.");
        puts(outbuf);
        
        fflush(stdout);             // 感覺在這裏可有可無
        puts(outbuf);               // 輸出
        
        return 0;
    }
```

<!--more-->

輸出如下:

```
    This is a test of buffer output.
    This is a test of buffer output.
    
    This is a test of buffer output.
    This is a test of buffer output.
```

程序很簡單，沒問題。於是我改了一下：

``` c
    /* test2.c */
    #include <stdio.h>
    int main(void)
    {
        char outbuf[512];
    
        setvbuf(stdout, outbuf, _IOFBF, 512);
    
        puts("This is a test of buffer output.");
        puts(outbuf);
    
        fflush(stdout);
        puts(outbuf);
    
        return 0;
    }
```

輸出如下:
```
    This is a test of buffer output.
    This is a test of buffer output.
    
    ***********
    *******
```
星號部分表示亂碼（或與**test1.c**輸出一樣，是不確定的)。這是爲什麼呢？後來我發現如果`outbuf`具有*靜態儲存期*時輸出卻與**test1.c**輸出一樣。自己能力不足去解釋這個問題（網上也沒有找到這樣的問題）， 在平時很活躍的羣問也沒人理。有人說在`clang`編譯與**test1.c**輸出一樣，我也測試在**tcc**編譯輸出也和**test1.c**一樣，我以爲是編譯器問題。

## debug ##
後來實在沒辦法， 又厚着臉皮去問**塵殤葉**前輩。。後來才知道：
* * *
因爲最後一個`puts(outbuf)`只是將`outbuf`的內容拷貝到`outbuf`512個字節中的某一處。當`main()`函數返回後，才會向終端打印字符。因爲`mian()`函數返回後會繼續執行一段c庫中的一些代碼。
* * *

那就是說`main(()`函數執行完，但程序並沒結束， 接着`main()`函數棧中的自動變量(*outbuf*)會被系統回收。如果被回收了很有可能亂碼或**Segmentation fault**。這就解釋了如果`outbuf`具有靜態儲存期(程序結束才被系統回收)就能像**test1.c**輸出一樣。如果在程序的末尾加上`fflush(stdout)`也能有同樣的效果。因把**緩衝區**刷新。將緩衝區中所有未寫入的數據被發送到`stdout`。其實也有關與編譯器， 不同的編譯器編譯出來的匯編。

## 最後 ##
謝謝**塵殤葉**的耐心解答。
