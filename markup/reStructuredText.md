# reStructuredText 介紹 & 語法

## 介紹
reStructuredText (以下簡稱 rst) 是一種輕量級標記式語言，主要用於撰寫 Python 技術文件。

## 語法
### 標題

rst 的標題使用 7-bits ASCII 符號來辨識，在任意文字(上)下方新增相同或更多的符號即可將文字標記為標題。

- rst 支援以下符號作為標題標記

```
! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~
```
    
**注意**

- 全形字被視為兩個字元
- 在標題上方新增標注符號僅增加可讀性，非必要
- 標題層級的判定以符號出現順序為基準
  - 第一種出現的出現的符號會被判定為 h1
  - 第二種即為 h2
- 建議的標記順序為：
  - `#`：上下標記，作為主題使用
  - `*`：上下標記，作為章使用
  - `=`：下標記，作為節使用
  - `-`：下標記，作為小節使用
  - `^`：下標記，作為小小節使用
  - `"`：下標記，作為段落使用

```rst
##########
This is h1
##########
**********
This is h2
**********

This is h3
==========
Normal text.

This is h3, too.
================
Normal text.

**********
Another h2
**********
```


### 特殊字型

- rst 提供以下特殊字型：
  - 斜體
  - 粗體
  - 專有名詞
  - 純文字

```rst
*斜體*
**粗體**
`專有名詞`
``純文字``
```

### 清單與列表

- 使用 `-`, `+`, `*` 可以建立清單
- 使用 阿拉伯數字或 `#` 可以建立數字清單
  - `#` 會接續前一個項目自動編號

**注意**
- 不同階層的清單，頭尾需要空行
- 不同層級的縮排為 2 個半形字元
- 數字清單無法建立階層

```rst
- foo
- bar

  - foobar
```

```rst
1. foo
2. bar
#. foobar
```

### 超連結

在字尾使用 `_` 可以對文字加入超連結，在字首使用則會設定超連結目標

**注意**

- 除了基本的超連結以外還有以下用法
  - 匿名超連結：使用 `__` 建立
  - 段落超連結：若想以一段文字為目標，用 `` ` `` 包覆即可為整段文字加入超連結
  - 簡單超連結：在段落超連結內加入 `<link>` 的話，會連至 `<link>` 之位址

- 超連結還有以下特性
  - 若未設定超連結目標，則會由內部章節抓取目標
  - 超連結之間可以互相導向

```rst
`簡單超連結 <https://www.python.org/>`_
Python_
anonymous__
`Everything is an object`_
超連結_
```

### 錨點

使用 `[]_` 可以建立錨點，除了普通文字外，還有以下特殊錨點

- 編號：使用 `[1]_` 或是 `[#]_` 可以建立編號錨點
- 符號：使用 `[*]_` 會自動產生特殊符號錨點

```rst
第一個錨點 [1]_
第二個 [2]_
自動編號 [#]_
符號標記 [*]_
符號標記2 [*]_
參考資料 [ref]_
```

### 連結目標

在句首使用 `..` 可以指定連結目標，如：

- 超連結目標
- 錨點目標
- 引用目標

```rst
.. _Python: https://www.python.org/
.. _`Everything is an object`: https://www.python.org/
.. [1] 第一個錨點
.. [2] 第二個錨點
.. [#] 自動編號
.. [*] 符號標記
.. [*] 符號標記2
```

### 特定標記

`..` 除了設定連結目標以外，還可以進行各種特定標記，如：

- 註解
- 注意事項

**注意**

- `..` 提供的特殊函數並非全部是 rst 原生功能，大部分為特定套件解釋時使用

```rst
..

    This is a comment.

.. note:: 
    
    This is a note.

.. attention::

    Thx for ur attention! 
```

### 替代文字

使用 `||` 可以引用替代文字

**注意**

- 定義替代文字處，需同時指定替代類型，較常見的替代類型有：
  - ``replace``: 替代為文字
  - ``image``: 替代為圖片

```rst
Hello, |something|!

|logo|

.. |something| replace:: world
.. |logo| image:: https://www.python.org/static/img/python-logo.png
```


### 圖片

接續替代文字，在嵌入圖片時有以下設定：

- `:height:` 圖片高度，單位見下方
- `:width:` 圖片寬度，單位為
  - `em`, `ex`, `px`, `in`, `cm`, `mm`, `pt`, `pc`
- `:alt:` 圖片無法顯示時的替代文字
- 更多設定參考 `html` 的 `<image>` 標籤

```rst
|logo|

.. |logo| image:: https://www.python.org/static/img/python-logo.png
    :height: 200px
    :width: 200px
```


### 程式碼區塊

`.. code-block:: <language>` 可以建立程式碼區塊

**注意**

- 程式碼內容需要上下空 1 列，且縮排 4 字元 


```rst
.. code-block:: py

    >>> print('Hello, world!')
    Hello, world!
```

### 純區塊

`::` 可以建立文字區塊

**注意**

- 區塊內容需要上下空 1 列，且縮排 4 字元 

```rst
::

    foo
```

### 文字區塊

於句首使用 `|` 可以建立文字區塊，文字區塊會跳脫原有分行格式，以輸入的樣式為主

```rst
| 這是文字區塊，
| 文字需快會跳脫原有的分行格式，
| 依照輸入的樣式為主。
```

### 標籤

以 `::` 包覆文字，可以將其建立為標籤

```rst
:Author: yveschen2718@gmail.com
```

### 分隔線

四個以上連續的 `-` 可以建立分隔線

```rst
foo

----

bar
```

### 定義

接續前一行，並以 4 個字元縮排可以建立為定義說明

```rst
說明：
    這是一段說明。
```

## 參考
- [Quick reStructuredText]


[Quick reStructuredText]: https://docutils.sourceforge.io/docs/user/rst/quickref.html
