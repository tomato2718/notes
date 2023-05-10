# [Python] Pytest 介紹
## 概要
Pytest 是以 Python 原生 `unittest` 模組為基底，擴增加強的元件化測試用套件，也是社群中最主要使用的元件化測試工具。Pytest 可以自動偵測測試集、將測試模組化執行，也提供了擴充套件讓使用者根據需求調整功能。

## 說明
### 安裝 pytest
#### 說明
直接使用 pip 安裝即可。
#### 操作
- 使用 pip 安裝。
```sh
>>> pip install pytest
```

### 使用 pytest
#### 說明
pytest 會自動偵測專案中所有使用 `test_*.py` 或 `*_test.py` 命名的檔案，並抓取其中以 `test_` 為前綴命名的函式進行測試，直接於專案根目錄執行 `pytest` 或 `python -m pytest` 指令即可。

雖然 pytest 會自動偵測專案目錄下所有檔案，但還是建議建立一個資料夾專門存放測試集。

#### 操作
- 建立 tests 資料夾以存放測試相關檔案。
```sh
.
├── MANIFEST.in
├── README.md
├── setup.py
├── tests/
│   ├── __init__.py # 宣告 tests 為 python package
│   ├── __main__.py # 見下方說明
│   └── conftest.py # 見下方說明
└── MyApp/
```

- 建立 `__init__.py`。

```py
'''
宣告該資料夾為 python package，可以為空檔案。
'''
```

- 建立 `__main__.py`。

```py
'''
雖然可以使用指令列參數設定 pytest，
但還是建議建立測試集入口檔案存放 plugins 或啟動參數。
'''

# 導入 pytest 模組
import pytest
from sys import argv

# 可以於此建立模組，詳細見官方文件
class MyPlugin:
    def pytest_sessionstart(self):
        pass
    def pytest_sessionfinish(self):
        pass

if __name__ == '__main__':
    # 由 argv 抓取要使用的參數，若未傳入則使用右方參數做為預設
    arg = argv[1:] if argv[1:] else ['--cache-clear', '--verbose']
    # 使用 pytest.main 啟動測試
    pytest.main(arg, plugins=[])
```

- 建立 `conftest.py`。

```py
'''
conftest 是用來儲存共用參數的檔案，詳細請參考文末官方文件連結。
'''
import pytest

# 帶有 fixture 裝飾的函式會被 pytest 自動抓取
@pytest.fixture(scope='package')
def euler_number() -> float:
    return 2.71828

@pytest.fixture(scope='package')
def pi() -> float:
    return 3.14159
```

- 建立測試 `test_*.py`。

```py
'''
使用 ``test_*.py`` 或 ``*_test.py`` 命名的檔案都會被視為測試集。
'''
import pytest

# 使用 test_ 命名的函式會自動進行測試
def test_pi(pi):
    # pytest 使用 assert 語句進行測試
    # 這行可以理解為：假設 pi 為 3.14159
    assert pi == 3.14159
```

- 執行測試。

```sh
# 查看說明
>>> pytest -h
# 指定 tests 資料夾開始測試
>>> pytest tests
# 測試時輸出詳細測試結果並移除 cache
>>> pytest tests --verbose --cache-clear
```


### 設計測試
#### 說明
元件化測試應該遵守以下原則：

1. **快速**：專案不該有過多的元件化測試，測試應該要能在數秒內執行完畢。
2. **獨立**：每個元件化測試應該要能獨立執行，同時不依賴 I/O 項目。
3. **可再現**：任何測試在不修改內容的情況下，應該有相同的結果。
4. **不費時**：撰寫測試的時間不該比專案長，程式碼應該以容易測試為首要設計目標。

#### 範例
- 假設資料夾如下：
```sh
.
├── tests/
│   ├── __init__.py
│   ├── __main__.py
│   ├── conftest.py
│   └── test_example.py
└── MyApp/
    └── example.py
```

- 專案中有一個函式
```py
'''
MyApp/example.py
'''

def reverse(lst: list) -> list:
    '''
    將輸入列表反轉並回傳的函示
    '''
    return lst[::-1]
```

- 與其對應的測試應撰寫為
```py
'''
tests/test_example.py
'''

# 導入 pytest 模組
import pytest

# pytest 會自動將專案根目錄加入 sys.path
# 因此可以直接導入專案資料夾下的模組
from MyApp.example import reverse

# 建議以 test_<被測者名稱> 進行命名
def test_reverse():
    # 建立要測試的項目
    lst = [1, 2, 3, 4, 5]
    # 使用 assert 語句建立測試
    assert reverse(lst) == [5, 4, 3, 2, 1]
```

- pytest 的功能眾多，更進一步的說明請參考 [官方文件][pytest]。

## 參考
- [pytest]
- [best practice]

[pytest]: https://docs.pytest.org/en/latest/contents.html
[best practice]: https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices