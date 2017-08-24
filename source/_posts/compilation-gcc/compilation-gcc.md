---
title: 編譯 gcc7.1.0
date: 2017-06-23 16:49:46
tags: gcc
---

## 下載依賴包
最近在看博客的時候無意中看到 `gcc 7.1.0` 出來了， 就想更新一下。 因之前我也嘗試編譯了 `gcc 6.3.0`

<!--more-->

我是在[清華大學鏡像源](https://mirrors.tuna.tsinghua.edu.cn/ "清華大學鏡像源") 下載了 gmp、mpfr、mpc、gcc的包。因第一次是直接在下在[gnu官網](http://ftp.gnu.org/gnu/ "gnu官網")，下載實在是太慢了。 而且上次編譯的時候還是直接是
``` bash
    ./download_prerequisites
    ./configure
    make
    make install
```
因爲這樣還要下載依賴包。 所以這次嘗試一下離線編譯， 自己下載依賴包編譯。 安裝過程我就不寫了， 因本人也是初學者。 感謝搜索引擎和[csdn](http://www.csdn.net/ "csdn")， 都是自己找的教程。 在羣問人屢招忽視， 靠人不如靠己啊。

## 編譯完後
`make` 過程很久, 中間也有出錯過， 經羣友提醒再次編譯， 而且我還在虛擬機裏面裝的。裝了兩個版本的 `gcc` 了，都沒有一次直接通過的。 裝完後在[github](https://github.com/tvaneerd/cpp17_in_TTs/blob/master/ALL_IN_ONE.md "github")找了一個 `c++17` 特性的代碼測試了一下， 因本人還不會 `c++` 拷貝一個完整簡單的。代碼如下:

``` c
    /* c++17 feature */

    #include <iostream>
    #include <string>
    using namespace std;

    struct Foo
    {
        int x = 0;
        std::string str = "wrold";
        ~Foo()
        {
            std::cout<<str<<endl;
        }
    };

    int main(void)
    {
        auto [i, s] = Foo();
        std::cout<<"hello "<<endl;
        s = "structured bindings";

        return 0;
    }
```

## 最後附上截圖

編譯命令 `g++ -std=c++1z -Wall source_file.c -o program.out` 其中的 `-std=c++1z` 是因編譯的是 `c++17` 特性的程序。

![source_file](https://github.com/ByXc01/Blog-image/raw/master/compilation_gcc/c%2B%2B17_feature.png "source")
