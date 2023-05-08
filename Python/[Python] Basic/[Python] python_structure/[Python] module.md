### module
一個 `.py` 就是一個 module，而一個 module 中可以包含了很多個 function 和 class
module 除了核心的功能以外，還有著一些讓開發者便於維護的細節，接下來會依序介紹

- 文件
    - module 的文件書寫於檔案最上方
    - 用來記錄這個檔案負責哪些事
    - module 的文件可以在 import 時被編譯器讀取，也可以透過 `__doc__` 呼叫

```py
'''
module document
'''
```

- `__all__`
    - 接在文件之後
    - 這份檔案被 `*` import 時，應該提供哪些功能
    - `from mymodule import *`
        - 一般情況盡量避免使用 `*`，會造成維護的困難
    - 建議在開發結束後再填寫，否則會需要多次更動
    - 預設為所有成員
        - 所有 import 的模組都會被再次 import 至其他模組
        - 不含前置底線成員

```py
'''
module document
'''

__all__ = ['my_func']
```

- import
    - 依序為 `__future__` -> 標準庫 -> 第三方模組 -> 本地模組
        - `__future__` 為[特殊模組][future]，提供了未來版本的部分功能
        - 習慣放置於 `__all__` 之前
    - 盡量以 `from a import A` 的方法導入
    - import 的詳細方法會在其他篇介紹

```py
'''
module document
'''

# __future__
from __future__ import print_function

__all__ = ['my_func']

# stdlib
import logging
from sys import argv

# third party
from yaml import safe_load

# local
from lib.toolbox import Toolbox
```

- global
    - 在這個模組中會用到的全域變數
    - 最常見的就是 logger

```py
'''
module document
'''

# __future__
from __future__ import print_function

__all__ = ['my_func']

# stdlib
import logging
from sys import argv

# third party
from yaml import safe_load

# local
from lib.toolbox import Toolbox

logger = logging.getLogget(__name__)
```

- functions and classes
    - 以上的內容都寫完之後，才開始寫模組的主要功能
    - 一個完整的 module，應該會長得像下方的樣子
```py
'''
module document
'''

# __future__
from __future__ import print_function

__all__ = ['my_func']

# stdlib
import logging
from sys import argv

# third party
from yaml import safe_load

# local
from lib.toolbox import Toolbox

logger = logging.getLogget(__name__)

class MyClass:
    '''
    Class doc string
    '''
    def __init__(self):
        '''
        Instance doc String
        '''
        pass

def my_fun():
    '''
    Function doc string
    '''
    pass
```

[future]: https://docs.python.org/3/library/__future__.html