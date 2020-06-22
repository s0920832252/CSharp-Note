---
tags: C# , Language
---

# String Interpolation & 格式化整理
## 前言
- String Interpolation 讓字串插入某些「特定字」. 可用來取代 String.Format , String Interpolation 比 String.Format 讓我們更容易表示愈顯示的字串格式.
- 差異
    - String.Format 的使用是 string.Format("{0} 的年齡是 {1}", name, age)) , 先佔位後 , 再輸入變數.
    - 但 string interpolation 則可以直接將變數放入佔位處 , 像是
        - $"{name} 的年齡是 {age}"
    - String.Format 變數位置擺的位置比較不直覺 , 所以位置順率可能會擺錯.
- $ 編譯後與 String.Format 編譯後相等
- 簡單範例
```C#
string name   = "City";
int    age    = 18;
double weight = 62.1;

// # old vs new 
Console.WriteLine("# old vs new ");
Console.WriteLine(string.Format("{0} 的年齡是 {1}", name, age)); // old
Console.WriteLine($"{name} 的年齡是 {age}");                     // new 
Console.WriteLine();

// # 格式化字串
Console.WriteLine("# 格式化字串");
// old
Console.WriteLine("## 舊的寫法");
Console.WriteLine(string.Format("{0} 的體重是 {1:##.000}", name, weight));
Console.WriteLine($"日期時間 {DateTime.Now: yyyy/MM/dd HH:mm:ss}");
Console.WriteLine(string.Format("{0,20} - 圓周率", Math.PI));
Console.WriteLine(string.Format("|{0,-10}|{1,10}|", "靠左對齊", "靠右對齊"));
// new 
Console.WriteLine("\n## 新的寫法");
Console.WriteLine($"{name} 的體重是 {weight:##.000}");
Console.WriteLine($"日期時間 {DateTime.Now:yyyy/MM/dd HH:mm:ss}");
Console.WriteLine($"{Math.PI,20} - 圓周率");
Console.WriteLine($"|{"靠左對齊",-10}|{"靠右對齊",10}|");
Console.WriteLine();

//逐字解譯字串常值
Console.WriteLine("# 逐字解譯字串常值");
Console.WriteLine($@"\ABC\DEF");
Console.WriteLine($@"\ABC\DEF {age} \n"); // 仍然可以插入變數
``` 


```C#
/// 輸出結果
# old vs new
City 的年齡是 18
City 的年齡是 18

# 格式化字串
## 舊的寫法
City 的體重是 62.100
日期時間  2020/05/31 14:20:04
   3.141592653589793 - 圓周率
|靠左對齊      |      靠右對齊|

## 新的寫法
City 的體重是 62.100
日期時間  2020/05/31 14:20:04
   3.141592653589793 - 圓周率
|靠左對齊      |      靠右對齊|

# 逐字解譯字串常值
\ABC\DEF
\ABC\DEF 18 \n
```

## 常見的數值格式
### 貨幣
```C#
Console.WriteLine($"{2-0:C1}"); // C1 代表為 2 後面補1個零
Console.WriteLine($"{2-0:C3}"); // C3 代表為 2 後面補3個零
Console.WriteLine($"{2-0:C6}"); // C6 代表為 2 後面補6個零

NT$2.0
NT$2.000
NT$2.000000
```
### 十進制 Decimal
- 帶選擇性負號的整數位數
```C#
Console.WriteLine($"{2-0:D1}"); // 有1個數字 , 若不夠 , 前面補零
Console.WriteLine($"{2-0:D3}"); // 有3個數字 , 若不夠 , 前面補零
Console.WriteLine($"{2-0:D6}"); // 有6個數字 , 若不夠 , 前面補零
Console.WriteLine($"{-2-0:D6}"); // 有6個數字 , 若不夠 , 前面補零

2
002
000002
-000002
```
### 指數標記
```C#
Console.WriteLine($"{12345.6789-0:E}"); //預設小點後六點 , 四捨五入
Console.WriteLine($"{12345.6789-0:E4}"); // 小數點後四位 , 四捨五入
Console.WriteLine($"{12345.6789-0:E10}"); // 小數點後十位 , 可能補零

1.234568E+004
1.2346E+004
1.2345678900E+004
```
### 用分號隔開的數字
```C#
Console.WriteLine($"{250000.1-0:N0}"); // 沒有小數
Console.WriteLine($"{250000.1-0:N}"); // 預設小數點後兩位
Console.WriteLine($"{250000.1-0:N3}"); // 小數點後三位
Console.WriteLine($"{250000.1-0:N6}"); // 小數點後六位

250,000
250,000.10
250,000.100
250,000.100000
```
### 十六進制
```C#
Console.WriteLine($"{0x1E - 0:X}"); // 預設1個佔位數字
Console.WriteLine($"{0x1E - 0:X1}"); // 1個佔位數字
Console.WriteLine($"{0x1E - 0:X3}"); // 3個佔位數字 , 可補零
Console.WriteLine($"{30 - 0:X1}"); // 1個佔位數字
Console.WriteLine($"{30 - 0:X3}"); // 3個佔位數字 , 可補零
Console.WriteLine($"{30 - 0:X6}"); // 6個佔位數字 , 可補零

1E
1E
01E
1E
01E
00001E
```
### 格式化百分比
```C#
Console.WriteLine($"{-0.24583-0:P}"); // 預設小數點後兩位 , 四捨五入
Console.WriteLine($"{-0.24583-0:P1}"); // 小數點後一位 , 四捨五入
Console.WriteLine($"{-0.24583-0:P3}"); // 小數點後3位 , 四捨五入

Console.WriteLine($"{0.24583-0:P}"); 
Console.WriteLine($"{0.24583-0:P1}");
Console.WriteLine($"{0.24583-0:P3}");

Console.WriteLine($"{10.3 - 0:P}");
Console.WriteLine($"{10.3 - 0:P1}");
Console.WriteLine($"{10.3 - 0:P3}");

-24.58%
-24.6%
-24.583%
24.58%
24.6%
24.583%
1,030.00%
1,030.0%
1,030.000%
```
## 自訂數值格式字串
### 零預留位置符號 "0" 
- 預留數字位置補零 , 但若變數無該位置 , 則補零 e.g. 1 vs 000 , 輸出 001
- 四捨五入 
```C#
var num = 123;
Console.WriteLine($"{num:0}");     // 預留一個 0 的位置
Console.WriteLine($"{num:000}");   // 預留三個 0 的位置
Console.WriteLine($"{num:00000}"); // 預留五個 0 的位置

Console.WriteLine($"{1.2378:0.0}");  // 預留個位數以及小數點後一個數字
Console.WriteLine($"{1.2378:0.00}");
Console.WriteLine($"{1.2378:00.00}");
123
123
00123
1.2
1.24
01.24
```
### 數字預留位置符號 "#"
- 預留數字位置 , 但若變數無該位置 , 則忽略 e.g. 1 vs ### , 依然輸出 1
- 四捨五入 
```C# 
var num = 123;
Console.WriteLine($"{num:#}");      // 預留一個數字的位置
Console.WriteLine($"{num:###}");    // 預留三個數字的位置
Console.WriteLine($"{num:#####}");  // 預留五個數字的位置
Console.WriteLine($"{1.2378:###.#}"); 
Console.WriteLine($"{1.2378:###.##}"); 
123
123
123
1.2
1.24
```
### 客製化電話格式
```C#
var phone = 0229007657;
Console.WriteLine($"{phone:(0#) ########}");
Console.WriteLine($"{phone:區域號碼是 (0#)  電話是 ########}");

(02) 29007657
區域號碼是 (02)  電話是 29007657
```


## 總結
- 使用此語法時 , $ 和 "" 中間不能有空白 , 不然編譯會發生問題.
- 要顯示「{}」時 , 使用雙大括號 , 意即要輸入「{{」和「}}」才能正常顯示「{」和「}」.
  - 例如：$"Name is={{name}}" --> Name is={name}.
- 當要使用三元運算式時 , 要使用「()」包起來.
  - var name = "王大明";
  - 例如：$"Name is=[{(name.Length > 2 ? "周論發" : name)}]" --> Name is=[周論發].
- C# 8.0 以前 , 若是需要同時使用 $ , @ 兩個符號. $ 要在 @ 的前面 


## 參考
[標準數值格式字串](https://docs.microsoft.com/zh-tw/dotnet/standard/base-types/standard-numeric-format-strings)
[C# 中的字串插補](https://docs.microsoft.com/zh-tw/dotnet/csharp/tutorials/string-interpolation)
[自訂數值格式字串](https://docs.microsoft.com/zh-tw/dotnet/standard/base-types/custom-numeric-format-strings)
[自訂日期與時間格式字串](https://docs.microsoft.com/zh-tw/dotnet/standard/base-types/custom-date-and-time-format-strings)

### Thank you! 

You can find me on

- [GitHub](https://github.com/s0920832252)
- [Facebook](https://www.facebook.com/fourtune.chen)

若有謬誤 , 煩請告知 , 新手發帖請多包涵

# :100: :muscle: :tada: :sheep: 
