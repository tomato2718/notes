### class
若是有一群 function 需要重複被使用，可以將他們都包進 class 中

-  一個 class 應該會長得像下段程式碼
    - class 的命名習慣使用大駝峰命名

```py
class MathTool:
    def __init__(self):
        pass

    def add(self, arg1: int, arg2: int) -> int:
        res = arg1 + arg2
        return res

    def subtract(self, arg1: int, arg2: int) -> int:
        res = arg1 - arg2
        return res
```

- 實體化
    - 若是一個 class 要被重複使用，可以先進行實體化
    - 可以把 class 當成一份範本，實體化就是從範本建立物件
    - 實體化後的 class 與原本的 class 不再有關聯性
    - 原則上，非工具類型的 class 都須要實體化後使用實體物件，避免對範本造成影響

```py
mytool = MathTool()
mytool.add(1, 1)
>> 2
```

- method 與 `self`
    - 在 class 中的 function，稱為 method
    - method 需要多帶一個參數 `self`，在 class 被實體化後這個參數代表著被實體化的物件本身
    - 就算沒有用到，還是必須要帶 `self` 這個參數
    - 若是物件內的 method 要呼叫其他 method，也會需要 `self`

```py
class Person:
    # 物件的 name 參數
    name = ''
    def __init__(self, name: str) -> None:
        # 把 mathod 接收到的 name 指派給物件的 name 參數
        self.name = name
        self.hello()

    def hello(self) -> None:
        print(self.name + ', hello!')

tom = Persno('Tom')
>>Tom, hello!
```

- `__init__(self)`
    - `__init__(self)` 是 python 指定的方法
    - 在 class 在被實體化時，會先執行一次
    - 只有在被實體化時，才會有效
    - 通常會用來把收到的參數指派給物件

```py
class Person:
    def __init__(self, name: str, sex: str, age: int) -> None:
        self.name = name
        self.sex = sex
        self.age = age

tom = Person('Tom', 'male', 30)
print(tom.sex)
>> male
print(tom.age)
>> 30
```

- 私有屬性
    - 若是有些 method 只提供給物件內部使用，不想讓其他人在外面呼叫，則需要加上前置雙底線
    - 此時該 method 只能在物件內被其他物件呼叫
    - 參數同樣也可以加上雙底線作為物件內部參數使用

```py
class Person:
    def __init__(self, name: str, sex: str, age: int, id: int) -> None:
        self.name = name
        self.sex = sex
        self.age = age
        self.__id = id
    def __something(self):
        print('do something')

tom = Person('Tom', 'male', 30, 12345)
tom.__something()
>> 'Person' object has no attribute '__something'
print(tom.__id)
>> 'Person' object has no attribute '__id'
```

- 靜態型別
    - 若是一個 method 不需要被實體化，就可以使用靜態型別
    - 靜態型別的 method 沒辦法呼叫物件內其他成員
    - 由於與物件無關了，所以也不需要 `self`
    - 通常用在工具類型的 method
    - 直接在 method 上加上 `@staticmethod` 即可

```py
class MathTool:
    def __init__(self):
        pass
    
    @staticmethod
    def add(arg1: int, arg2: int) -> int:
        res = arg1 + arg2
        return res

    def subtract(self, arg1: int, arg2: int) -> int:
        res = arg1 - arg2
        return res
```

- 類別型別
    - 通常用在不打算實體化的 class，像是工具包
    - 類別型別的方法可以使用 class 成員，但不能使用物件成員
    - 此時 `self` 會指向類別而非物件，因此習慣上會改命名為 `cls`
    - 直接在 method 上加上 `@classmethod` 即可

```py
class MathTool:
    def __init__(self):
        pass
    
    @classmethod
    def add(cls, arg1: int, arg2: int) -> int:
        res = arg1 + arg2
        return res

    @classmethod
    def subtract(cls, arg1: int, arg2: int) -> int:
        res = arg1 - arg2
        return res

MathTool.add(1, 1)
>> 2
```

- 簡單比較三種型別
    - 基本(不帶裝飾)：可以取用類別與物件所有成員
    - 類別(`@classmethod`)：可以取用類別成員、不能取用物件成員
    - 靜態(`@staticmethod`)：不能取用任何成員

- 繼承與 `super()`
    - python 的 class 一次可以繼承多個父類別
    - 直接於 class 後方加上 `()` 就能繼承類別
    - `super()` 可以用來呼叫父類別的成員
    - 最常見的用法就是創建 customize exceptions

```py
# Exception 是 pyhton 例外的總類
class MyException(Exception):
    def __init__(self, message: Any):
        '''
        Custom exception
        '''
        super().__init__(message)
```

- metaclass
    - 如果說 class 是定義物件的範本，metaclass 就是定義 class 的範本
    - 牽涉到類別的創建規則，因此絕大多數的時間都不會使用到
    - 通常只有在框架級別的專案會使用
    - metaclass 必須繼承自 `type`

```py
'''
範例僅節錄部分 code
這是用來產生 SGR 的 class
修改了取得參數的規則，在請求參數的時候會自動補上前後綴
並且額外定義了 getvalue 方法，用來取得無前後綴的值
'''
class StyleMeta(type):
    # 內建取得成員的方法
    def __getattribute__(self, __name: str) -> Any:
        # 使用 object 原始方法取得成員
        res = object.__getattribute__(self, __name)
        # 如果目標為字串
        if isinstance(res, str):
            # 加上前後綴
            res = '\033[' + res + 'm'
        # 回傳結果
        return res

    # 自行定義之取得原生值的方法
    # 運作流程為：
    # 請求 getvalue
    # 觸發  __getattribute__ 來取得名為 getvalue 的成員
    # __getattribute__ 取得的成員 getvalue
    # 判斷 type 為 method，因此不加上前後綴直接回傳
    # 取得 getvalue 並開始執行 getvalue
    # getvalue 使用原生方法取得成員
    # 回傳成員
    def getvalue(self, __name: str) -> Any:
        # 如果成員存在
        if hasattr(self, __name):
            # 取得成員並回傳
            return object.__getattribute__(self, __name)
        else:
            # 否則回傳空字串
            return ''

class Style:
    def __new__(cls, fg: str=None, bg: str=None) -> str:
        res = []
        if fg:
            res.append(cls.FG.getvalue(fg))
        if bg:
            res.append(cls.BG.getvalue(bg))
        res = ';'.join(res)
        return '\033[' + res + 'm'
    # 使用 StyleMeta 作為規則創建 FG 類別
    class FG(metaclass = StyleMeta):
        BLACK   = '30'
        RED     = '31'
        GREEN   = '32'
        YELLOW  = '33'
        BLUE    = '34'
        MAGENTA = '35'
        CYAN    = '36'
        WHITE   = '37'

    class BG(metaclass = StyleMeta):
        BLACK   = '40'
        RED     = '41'
        GREEN   = '42'
        YELLOW  = '43'
        BLUE    = '44'
        MAGENTA = '45'
        CYAN    = '46'
        WHITE   = '47'

Style.FG.RED
>> \033[31m
```

- magic methods
    - 帶有前後雙底線的方法，稱為 magic method
    - 除了 `__init__` 以外，還有很多的 magic methods
    - 可以參考[官方文件][magic]

- 文件
    - class 一樣可以加入 doc
    - 主要分為三個部分

```py
class MyClass:
    '''
    class 的文件直接寫在 class 下
    '''
    def __init__(self):
        '''
        實體化的文件寫在 __init__ 裡
        '''
        pass
    def a_method(self):
        '''
        method 的文件寫在 method 裡
        '''
        pass
```

[magic]: https://docs.python.org/3/reference/datamodel.html#special-method-names