## Python Set, Dict 與 collections 類常用方法

### set
- 簡介
    - 基本資料型態
- 特色
    - 無序
    - 不重複
    - iterable
- 常用方法
```py
# 創建 set
bar = set()

# 創建 set
# 注意： foo = {} 會創建 dict 非 set
foo = {1,2,3,4,5}

# 加入元素
foo.add()

# 移除元素，若元素不存在則會拋出錯誤
foo.remove()

# 移除元素，若元素不存在則略過
foo.discard()

# 隨機移除並 return 一個元素
a = foo.pop()

# 清除 set
foo.clear()
```
- 文件
    - [點我][set]

### frozenset
- 簡介
    - 同 `set`，但創建後不可再修改
- 特色
    - 無序
    - 不重複
    - iterable
- 文件
    - [點我][set]

### dict
- 簡介
    - 基本資料型態
- 特色
    - key-value 對
    - key 不重複
    - iterable
    - 無序
- 常用方法

```py

# 三種方法皆可創建 dict
foo = {}
foo = {'tomato': 20}
bar = dict()

# 設定 key-value 對
foo['apple'] = 50

# 取得 value，若不存在則會拋出錯誤
foo['apple']

# 取得 value，若不存在則回傳預設值 None
# 效率上較不建議使用，參考下方 defaultdict
foo.get('banana')

# 清空 dict
foo.clear()

# 取得 dict 的所有 key-value 對
foo.items()
```

- 文件
    - [點我][dict]

### defaultdict
- 簡介
    - 同 `dict`，但給予每個 key 預設值
    - 對空值的處理效率比 dict.get() 好
    - 因此有空值時會建議使用 defaultdict 而非 dict
- 特色
    - key-value 對
    - key 不重複
    - iterable
    - 無序
- 常用方法

```py
# 方法基本與 dict 相同

# 需要先 import collections.defaultdict
from collections import defaultdict

# 創建一個預設值為 int 的 defaultdict
bar = defaultdict(int)
# 以 dict 為基底，創建預設值為 str 的 defaultdict
foo = defaultdict(str, {'a':'apple', 'b':'banana'})

# 取得 foo 中 tomato 的值
# 此時由於 defaultdict(str) 會給予所有 key 預設值，因此會返回 ''
foo['tomato']
```

- 文件
    - [點我][defaultdict]

### Counter
- 簡介
    - 由 iterable 物件創建計數 dict
- 特色
    - key-value 對
    - key 不重複
    - iterable
    - 無序
- 常用方法

```py
# 方法基本與 dict 相同

# 需要先 import collections.Counter
from collections import Counter

# 範例字串
string = 'aaabbbccdefggh'

# 由 string 產生計數器
# 所有 key 之預設值為 0
c = Counter(string)
```

- 文件
    - [點我][Counter]



[set]: https://docs.python.org/3/library/stdtypes.html?highlight=tuple#set-types-set-frozenset
[dict]: https://docs.python.org/3/library/stdtypes.html?highlight=tuple#mapping-types-dict
[defaultdict]: https://docs.python.org/zh-tw/3/library/collections.html#defaultdict-objects
[Counter]: https://docs.python.org/zh-tw/3/library/collections.html#counter-objects