# Python Basic: Code& Function
## Code
code 是最基本的單位，一行 code 可以執行一個動作。

```py
print('Hello, world!')
>> Hello, world!
```

## Function 與 Method
function 與 method 其實是一樣的東西，差別只在寫在 class 內的會稱為 method、獨立在外的稱為 function。

### function 的結構
一個 function 會長的像下段程式碼
- 若該 function 不回傳任何東西，直接移除 return 即可
- function 的命名習慣使用全小寫與底線分隔

```py
def add(arg1, arg2):
    res = arg1 + arg2
    return res

add(1, 1)
>> 2
```

### 資料型態提示
由於 python 沒有強制宣告資料型態，其他人可能會不知道你的參數和回傳型態，造成維護上的困難，因此 python 提供了註解功能。

- `arg1` 冒號後的類別即為該參數應有的資料型態，而末尾 `-> int` 則是該 function 回傳的資料型態
> **注意**：這邊的提示只有**註解**功能，並不會強制指定(或改變)資料型態。

```py
def add(arg1: int, arg2: int) -> int:
    res = arg1 + arg2
    return res
```

#### `|` 與 Any 型態
Python 是動態語言，因此傳入的參數可以是不同種型態，此時會無法使用上述方法加入提示。所以會需要其他方式進行提示

- 在新版本的 python 中，可以使用 `|` 來提示多種型態
- 較舊版本可以提示為 `Any`

```py
# | 的使用方法
def add(arg1: int|float, arg2: int|float) -> int|float:
    res = arg1 + arg2
    return res

# Any 的使用方法
# 由於 Any 並非內建型態，因此需要先從型態標準庫中導入
from typing import Any

def add(arg1: Any, arg2: Any) -> Any:
    res = arg1 + arg2
    return res
```

### 參數
#### 預設值
Python 也可以給予參數預設值，只需要在參數後面加上 `=` 就好。

> **注意:** 有預設值的參數必須放在沒預設值的後方
```py
def add(arg1: int, arg2: int, arg3: int = 10) -> int:
    # 若呼叫時 arg3 未被傳入，會被設定為 10
    res = arg1 + arg2 + arg3
    return res

add(1, 1)
>> 12
```

#### 不指名參數
`*args` 可以將所有傳入的不指名參數都收進 `args` 中。

- `args` 的資料型態為 `list`。

```py
def add(arg1: int, arg2: int, *args, arg3: int = 10) -> int:
    res = arg1 + arg2 + arg3
    for i in args:
        res += i
    return res

add(1, 2, 3, 4, 5, arg3=6)
>> 21
```

#### 額外指名參數
`**kwargs` 可以將所有傳入的指名參數都收進 `kwargs`

- `kwargs` 的型態為 `dict`。

```py
def add(arg1: int, arg2: int, *args, arg3: int = 10, **kwargs) -> int:
    res = arg1 + arg2 + arg3
    for i in args:
        res += i
    for i in kwargs:
        res += kwargs[i]
    return res

s = add(1, 2, 3, 4, 5, arg3=6, arg4=7, arg5=8)
>> 36
```

### docstring
docstring 是用來撰寫 function 介紹的字串，使用連續引號來寫 docstring。

- docstring 使用 reStructuredText 格式撰寫
- docstring 可以被多數編輯器抓取，也可以手動用 `__doc__` 呼叫

```py
def add(arg1: int, arg2: int, *args, arg3: int = 10, **kwargs) -> int:
    '''
    Add up all input arguments.
    '''
    res = arg1 + arg2 + arg3
    for i in args:
        res += i
    for i in kwargs:
        res += kwargs[i]
    return res
```
```py
>>> add.__doc__
Add up all input arguments.
```
