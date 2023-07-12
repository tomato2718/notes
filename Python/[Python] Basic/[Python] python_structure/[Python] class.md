# Python Basic: Class
## class
若是有一群 function 需要重複被使用，可以將他們都包進 class 中。

- class 的命名習慣使用大駝峰命名。

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

### 實體化
可以把 class 理解為一份範本，實體化就是從範本建立物件。
- 實體化後的 class 與原本的 class 不再有關聯性。
- 原則上非工具類型的 class 都須要實體化後使用實體物件，避免對範本造成影響。

```py
mytool = MathTool()
mytool.add(1, 1)
>> 2
```

### method 與 `self`
在 class 中的 function，稱為 method。

- method 需要多帶一個參數 `self`，在 class 被實體化後這個參數代表著被實體化的物件本身。
- 就算沒有用到，還是必須要帶 `self` 這個參數。

```py
class Person:
    def __init__(self, name: str) -> None:
        # 把 mathod 接收到的 name 指派給物件的 name 參數
        self.name = name
        # 呼叫自身的 hello() 方法
        self.hello()

    def hello(self) -> None:
        print(self.name + ', hello!')

tom = Person('Tom')
>> Tom, hello!
```

### 建構式
使用 `__init__()` 可以定義建構式。

- 在 class 在被實體化時，會先執行一次建構式。
- 通常會用來進行類別的初始化設定，像是把收到的參數指派給物件等。

```py
class Person:
    def __init__(self, name: str, gender: str, age: int) -> None:
        self.name = name
        self.gender = gender
        self.age = age

tom = Person(
    name='Tom',
    gender='male',
    age=30,
)
print(tom.gender)
>> male
print(tom.age)
>> 30
```

### 私有屬性
若是有些 method 只提供給物件內部使用，不想讓其他人在外面呼叫，則需要加上前置雙底線。

- 此時該 method 只能在物件內被其他物件呼叫。
- 參數同樣也可以加上雙底線作為物件內部參數使用。

```py
class Person:
    def __init__(self, name: str, sex: str, age: int, id_: int) -> None:
        self.name = name
        self.sex = sex
        self.age = age
        self.__id = id_
    def __something(self):
        print('do something')

tom = Person(
    name='Tom',
    gender='male',
    age=30,
    id_=12345,
)
tom.__something()
>> 'Person' object has no attribute '__something'
print(tom.__id)
>> 'Person' object has no attribute '__id'
```

### 靜態型別
若是一個方法不需要操作物件的成員，就可以使用靜態型別。

- 靜態型別的 method 沒辦法呼叫物件內其他成員。
- 由於與物件無關了，所以也不需要 `self`。
- 通常用在工具類型的方法。

```py
class MyClass:
    
    secret_number = 3
    def __init__(self):
        self.secret_number = 5
    
    # 直接在方法上面加上 @staticmethod 即可
    @staticmethod
    def print_secret():
        print(secret_number)
    
foo = MyClass()
foo.print_secret()
>> NameError: name 'secret_number' is not defined
```

### 類別型別
類別型別的方法可以使用 class 成員，但不能操作物件成員。

- 此時 `self` 會指向類別而非物件，因此習慣上會改命名為 `cls`

```py
class MyClass:
    
    secret_number = 3
    def __init__(self):
        self.secret_number = 5
    
    # 直接在方法上面加上 @classcmethod 即可
    @classmethod
    def print_secret(cls):
        print(cls.secret_number)
    
foo = MyClass()
foo.print_secret()
>> 3
```

### 簡單比較三種型別

- 基本(不帶裝飾)：可以取用類別與物件所有成員。
- 類別(`@classmethod`)：可以取用類別成員、不能取用物件成員。
- 靜態(`@staticmethod`)：不能取用任何成員。

### 繼承與 `super()`
python 的類別一次可以繼承多個父類別，如果父類別有相同的方法，會由最先繼承的 (最左邊) 為優先。

- 直接於 class 後方加上 `()` 就能繼承類別。
- 子類別可以直接使用父類別的成員。
- 若子類別覆寫了父類別的成員，可以使用 `super()` 呼叫未經覆寫的父類別的成員。
- 最常見的用法就是創建 customize exceptions。

> **此處僅介紹最基本的繼承。**

```py
# Exception 是 pyhton 例外的總類
class MyException(Exception):
    def __init__(self, message: Any):
        '''
        Custom exception
        '''
        super().__init__(message)
```

### metaclass
如果說 class 是定義物件的範本，metaclass 就是定義 class 的範本。

這部分牽扯到較深的運作邏輯，在此就先不解說。

### magic methods
帶有前後雙底線的方法，稱為魔法方法 (magic method)。魔術方法是 Python 語言內建的方法，在自己定義新方法時不建議用此方式命名。

- 除了 `__init__` 以外，還有很多的 magic methods。
- 可以參考[官方文件][magic]。


### docstring
class 一樣可以加入 doc，參考下方範例。

```py
class MyClass:
    '''
    類別的文件直接寫在類別下，說明這個類別的用途。
    '''
    def __init__(self):
        '''
        建構式的作用與類別通常會相同，所以不用特別說明，只要標明是建構式就好。
        '''
        pass
    def a_method(self):
        '''
        各個方法的說明跟函式相同，參考函式的寫法即可。
        '''
        pass
```

[magic]: https://docs.python.org/3/reference/datamodel.html#special-method-names