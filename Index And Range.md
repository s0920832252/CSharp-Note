---
tags: C# , Language
---

# Index & Range

## 前言
- Index & Range 是 C# 8.0 才提供的功能 , 其概念其實在別的語言早就有了. (e.g. Python) . 
- Index 和 Range 提供簡潔的語法 , 用於存取序列中的指定位置的單一元素或範圍
- Index 可以從後面開始數起.
- Range **目前**只能用在下列 (沒有 List)
    - 陣列
    - 字串
    - Span
    - ReadOnlySpan
## Index
```C#
public struct Index : IEquatable<Index>
{
    public Index(int value, bool fromEnd = false);
    
    public static Index Start => new Index(0);
    public static Index End => new Index(~0);
    public static Index FromStart(int value);
    public static Index FromEnd(int value);
    public int Value { get; }
    public bool IsFromEnd { get; }
    public int GetOffset(int length);
    public override bool Equals(object? value);
    public bool Equals(Index other);
    public override int GetHashCode();
    public static implicit operator Index(int value);
    public override string ToString();
}
```
- Index 是 readonly struct
- Index 由 0 開始計算
- 由 Index 的建構子可發現 , 索引存在正負的概念.
    - 第一個參數 value , 代表位置
    - 第二個參數 fronEnd , 代表此位置是否為由後往前推算

### 概念
```C#
var words = new string[]
{
                // index from start    index from end
    "The",      // 0                   ^9
    "quick",    // 1                   ^8
    "brown",    // 2                   ^7
    "fox",      // 3                   ^6
    "jumped",   // 4                   ^5
    "over",     // 5                   ^4
    "the",      // 6                   ^3
    "lazy",     // 7                   ^2
    "dog"       // 8                   ^1
};              // 9 (or words.Length) ^0
```

### 建立方式
- 建構式
    ```C#
    var index1 = new Index(3, false);  // 表第'3'個位置
    var index2 = new Index(3, true);   // 表倒數第三個位置
    ```    
- 靜態方法
    ```C#
    var index1 = Index.FromStart(3);     // 表第'3'個位置
    var index2 = Index.FromEnd(3);   // 表倒數第三個位置
    ```
- 靜態屬性    
    ```C#
    var index1 = Index.Start; // 0   , 表第零個位置
    var index2 = Index.End; // ^0 == set's length  , 表第  set's length 個位置
    ```
- 隱性轉換
    ```C#
    var index1 = 3;    // 表第'3'個位置
    var index2 = ^3;   // 表倒數第三個位置
    ```

### Index 範例
```C#
var list = new List<int> { 9, 8, 7, 6, 5, 4, 3, 2, 1 };

// 建構式
var index1 = new Index(3, false);
var index2 = new Index(3, true);

// 隱性轉換
var index3 = 3;
var index4 = ^3;

// 靜態方法            
var index5 = Index.FromEnd(1);
var index6 = Index.FromStart(1);

// 靜態屬性
var index7 = Index.Start;
var index8 = Index.End; // ^0

Console.WriteLine(list[index1]);
Console.WriteLine(list[index2]);
Console.WriteLine(list[index3]);
Console.WriteLine(list[index4]);
Console.WriteLine(list[index5]);
Console.WriteLine(list[index6]);
Console.WriteLine(list[index7]);
//Console.WriteLine(list[index8]); // Throw OutOfRangeException

// 輸出結果
6
3
6
3
1
8
9
```

### GetOffset
- Calculate the offset from the start using the giving collection length.
```C#
var index = Index.FromStart(2);
Console.WriteLine($"Offset : {index.GetOffset(10)}");
Console.WriteLine($"Offset : {index.GetOffset(1)}");

var index2 = ^2;
Console.WriteLine($"Offset : {index2.GetOffset(10)}");
Console.WriteLine($"Offset : {index2.GetOffset(1)}");

// 輸出結果
Offset : 2
Offset : 2
Offset : 8
Offset : -1
```
## Range
```C#
public readonly struct Range : IEquatable<Range>
{
    public Index Start { get; }
    public Index End { get; }
    public Range(Index start, Index end);
    public override bool Equals(object? value);
    public bool Equals(Range other);
    public override int GetHashCode();
    public override string ToString();
    public static Range StartAt(Index start) ;
    public static Range EndAt(Index end);
    public static Range All => new Range(Index.Start, Index.End);
    public (int Offset, int Length) GetOffsetAndLength(int length);
}
```
- Range 是 readonly struct
- [ startIndex ... endIndex ]  ,  endIndex 不包含 End Index  (上包 下不包)
- startIndex 必須小於 endIndex . **目前**尚不支援倒著數

### 建立方式
- 建構式
    ```C#
    var range = new Range(1, ^2);
    ```
- 靜態方法
    ```C#
    var startAt = Range.StartAt(1); // 從位置 1 開始直到最後
    var endAt = Range.EndAt(1);     // 在位置 1 以前 (不包含位置 1 )
    ```
- .. 運算子
    ```C#
    var range = 1..3;
    ```
### Range 簡單範例
```C#
var words = new string[]
{
        // index from start    index from end
        "The",    // 0                   ^9
        "quick",  // 1                   ^8
        "brown",  // 2                   ^7
        "fox",    // 3                   ^6
        "jumped", // 4                   ^5
        "over",   // 5                   ^4
        "the",    // 6                   ^3
        "lazy",   // 7                   ^2
        "dog"     // 8                   ^1
};

var start = 2 ;
foreach (var s in words[start..4])
{
    Console.WriteLine(s);
}

// 輸出結果
brown
fox
```
### Range 範例
```C#
var words = new string[]
{
        // index from start    index from end
        "The",    // 0                   ^9
        "quick",  // 1                   ^8
        "brown",  // 2                   ^7
        "fox",    // 3                   ^6
        "jumped", // 4                   ^5
        "over",   // 5                   ^4
        "the",    // 6                   ^3
        "lazy",   // 7                   ^2
        "dog"     // 8                   ^1
};
Console.WriteLine();

var range = new Range(0, ^8);
var ranges = new List<Range>
{
        range,
        Range.StartAt(7),
        Range.StartAt(^2),
        Range.EndAt(2),
        Range.EndAt(^7),
        {..^7},
        {7..},
        {4..^3},
        {1..3},
        {..},
};

foreach (var range1 in ranges)
{
    foreach (var str in words[range1])
    {
        Console.WriteLine(str);
    }
    Console.WriteLine("===========================");
}
```
##### 輸出結果
```
The
===========================
lazy
dog
===========================
lazy
dog
===========================
The
quick
===========================
The
quick
===========================
The
quick
===========================
lazy
dog
===========================
jumped
over
===========================
quick
brown
===========================
The
quick
brown
fox
jumped
over
the
lazy
dog
===========================
```

### Range OutOfRangeException
```C#
var words = new string[]
{
        // index from start    index from end
        "The",    // 0                   ^9
        "quick",  // 1                   ^8
        "brown",  // 2                   ^7
        "fox",    // 3                   ^6
        "jumped", // 4                   ^5
        "over",   // 5                   ^4
        "the",    // 6                   ^3
        "lazy",   // 7                   ^2
        "dog"     // 8                   ^1
};

var range = words[8..10]; // 會拋出例外
```

## 參考
[C# 8.0 搶先看 -- Ranges and Indicies (2) Range](https://dotblogs.com.tw/billchung/2018/12/26/232311)    
[C# 8.0 搶先看 -- Ranges and Indicies (1) Index](https://dotblogs.com.tw/billchung/2018/12/24/220436)    
[[C#] 8.0 新功能 - 索引與範圍](https://blog.kevinyang.net/2020/01/16/csharp-8-index-range/)    
[索引和範圍](https://docs.microsoft.com/zh-tw/dotnet/csharp/whats-new/csharp-8#indices-and-ranges)    
### Thank you! 

You can find me on

- [GitHub](https://github.com/s0920832252)
- [Facebook](https://www.facebook.com/fourtune.chen)

若有謬誤 , 煩請告知 , 新手發帖請多包涵

# :100: :muscle: :tada: :sheep: 
