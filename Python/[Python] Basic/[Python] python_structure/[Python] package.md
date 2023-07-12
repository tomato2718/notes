# Python Basic: Package
## Package
在 python 中，package 直接由資料夾建立，資料夾內多個 module 可以組合成 package

### 架構
一個 Python Package 會長得像這樣

```sh
MyPackage
├── __init__.py
├── __main__.py
├── _moduleA.py
└── _moduleB.py
```

### `__init__.py`
`__init__.py` 是組成 package 的關鍵，任何帶有 `__init__.py` 的資料夾都會被視為 python package。

- 在資料夾做為 package 被 **import** 時，會執行此檔案
    - 若沒有要執行的內容也可以為空。
- 透過設計此檔案，可以避免外部使用者 import 不相關模組

```py
'''
MyPackage/__init__.py
'''
__all__ = [
    'foo',
    'bar',
]

import _moduleA
from _moduleB import bar

# 將 _moduleA 的 foo function 指派為此 package 的 foo
# 注意不帶括號 _moduleA.foo 才是指派物件本身
# _moduleA.foo() 會呼叫該物件
foo = _moduleA.foo
```

```py
'''
外部程式
'''

# 可以直接導入 MyPackage 資料夾
# 實際導入的內容會是 MyPackage/__init__.py
from MyPackage import (
    foo,
    bar,
)

foo()
bar()
```

### `__main__.py`
在資料夾被做為模組 **執行** 時，會執行此檔案。

> **此處的模組是消歧義，實際上是指 Package。**
```py
'''
MyPackage/__main__.py
'''

print('hello, world!')
```

- 使用 `python -m` 來執行模組。

```sh
>>> python -m MyPackage
hello, world!
```

### Module
python 並沒有規定模組命名方式，但有建議遵守幾條規則。

- 命名一率使用全小寫單詞。
- 盡量避免以兩個以上單詞命名。
    - 不得已時使用底線分隔：`my_module.py`。
- 前置底線的模組通常代表模組內部使用，不提供外部導入。
    - `_utils.py`。
    - **無法實際防止模組被導入**。
    - 出於對開發者的尊重，建議遵守此規則。