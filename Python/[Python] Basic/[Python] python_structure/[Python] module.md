# Python Basic: Module
## Module
一個 `.py` 檔案就是一個 module，而一個 module 中可以包含了很多個 function 和 class。

module 除了核心的功能以外，還有著一些讓開發者便於維護的細節，接下來會依序介紹。

### docstring
module 一樣有 docstring，書寫於檔案最上方。

- docstring 可以在 import 時被編輯器讀取，也可以透過 `__doc__` 呼叫。

```py
'''
Module docstring.
'''
```

### `__all__`
接在文件之後，使用 `__all__` 來表示這個模組要提供給其他模組使用的類別或函式。

- 建議在開發到一段落再填寫，否則會需要多次更動。
- 未被宣告時，預設值為所有成員。
    - 不含前置底線成員。

```py
'''
Module docstring.
'''

__all__ = ['my_func']
```

### import
建議的導入順序為：

`__future__` -> 標準庫 -> 第三方模組 -> 本地模組

- 可以在不同類別之間加入空行，方便維護者辨識。
- `__future__` 為[特殊模組][future]，提供了舊版本 Python 能使用新版本的部分功能。
    - `__future__` 習慣在 `__all__` 之前宣告。

```py
'''
Module docstring.
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
from .lib.toolbox import Toolbox
```

### 全域變數
直接在 module 層級宣告的變數，可以當成全域變數使用。只要在相同模組當中都能直接呼叫。

- 最常見的就是 logger。

```py
'''
Module docstring.
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
from .lib.toolbox import Toolbox

logger = logging.getLogger(__name__)
```

### 函式與類別
函式和類別是模組的主要功能，在上述的前置準備都完成之後，才會開始撰寫。

- 一個完整的模組，應該會長得像下方的樣子。

```py
'''
Module docstring.
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
from .lib.toolbox import Toolbox

logger = logging.getLogget(__name__)

class MyClass:
    '''
    Class docstring.
    '''
    def __init__(self):
        '''
        Constructor method.
        '''
        pass

def my_func():
    '''
    Function docstring.
    '''
    pass
```

[future]: https://docs.python.org/3/library/__future__.html