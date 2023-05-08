### package
在 python 中，package 直接由資料夾建立，資料夾內多個 module 可以組合成 package

- 架構
    - 一個 Python Package 會長得像這樣

```shell
MyPackage
├── __init__.py
├── __main__.py
├── _moduleA.py
└── _moduleB.py
```

- `__init__.py`
    - 組成 package 的關鍵
    - 任何資料夾中帶有 `__init__.py` 都會被視為 python package
    - 在資料夾被 **import** 時，會執行此檔案
        - 也可以為空
    - 透過設計此檔案，可以避免外部使用者 import 不相關模組

```py
## MyPackage/__init__.py
__all__ = ['foo', 'bar']

import _moduleA
from _moduleB import bar

# 將 _moduleA 的 foo function 指派為此 package 的 foo
# 注意不帶括號 _moduleA.foo 才是指派物件本身
# _moduleA.foo() 會執行該物件
foo = _moduleA.foo
```

```py
## 外部程式

# 可以直接 import MyPackage 資料夾
# 實際 import 的內容會是 MyPackage/__init__.py
from MyPackage import foo, bar

foo()
```

- `__main__.py`
    - 在資料夾被**執行**時，會執行此檔案


```py
## MyPackage/__main__.py

print('hello, world!')
```

```shell
python3 MyPackage
>> hello, world!
```

- modules
    - python 並沒有指定模組命名方式
    - 習慣上會以底線 `_` 做區隔
        - **沒有**實際作用
    - 帶前置底線的模組通常代表內部使用
    - 直接命名則是供外部使用

### 總結
- 為了降低開發與維護的負擔，coding 時建議盡可能把功能拆開
    - 一個 function 只做一件事
        - 加法 (code)
    - 一個 class 只做一類事
        - 加 (method)
        - 減 (method)
        - 乘 (method)
        - 除 (method)
    - 一個 module 只負責一個功能
        - 微分 (class)
        - 積分 (class)
    - 一個 package 只做一個領域的事
        - 微積分 (module)
        - 線性代數 (module)