# PySnooper 簡易操作說明
## 概要
PySnooper 是提供 pyhton 開發過程 debug 的第三方工具，可以深入追蹤程式碼的執行狀況降低 dubug 難度，此篇文章介紹 PySnooper 的基本使用方法。

## 操作
- 安裝 PySnooper

```sh
>>> pip install pysnooper
```

- 直接 import 

```py
import pysnooper
```

- 在想要追蹤的 function 加上裝飾器即可使用

```py
@pysnooper.snoop()
def foo():
    return bar
```

- 或是針對想追蹤的程式碼區塊使用 `with` 進行追蹤

```py
with pysnooper.snoop():
    print('foo')
    bar = 'bar'
```

- snoop() 常用功能
    - `prefix=str`: 在追蹤結果前加上指定 prefix
    - `normalize=True`: 不顯示機器相關資訊
    - `depth=int`: 指定追蹤深度
        - 注意若同時使用其他裝飾器，每個裝飾都會加深一層

- 更多功能請參考官方文件

## 參考
- [官方文件][1]

[1]: https://github.com/cool-RR/PySnooper