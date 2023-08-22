# [Python] Setuptools 與 setup\.py
## 概要
Setuptools 是一個用於分發 python 專案的工具，可以透過使用此工具將專案打包為單一 `.whl` 檔，攜帶至目標環境快速部署。

## 說明
### 安裝 Setuptools
#### 說明
直接使用 pip 安裝即可。

#### 操作
- 使用 pip 安裝。
```sh
>>> pip install setuptools
```

### 建立 setup.py
#### 說明
`setup.py` 是 setuptools 用於辨識要進行哪些動作的檔案，應該放置於專案的根目錄，並且根據 [官方文件][keywords] 中提供的項目進行設定。

#### 設定檔
- `setup.py` 的撰寫方式，可以參考此設定檔。
```py
# 引入需要的模組
from setuptools import setup, find_namespace_packages

setup(
    ### 專案資訊區塊 ###
    name = 'MyProject',
    version = '1.0.0',
    author = 'foo@example.com',
    maintainer = 'bar@example.com',
    description = 'this is an example project',
    ### 專案資訊結束 ###
    # 使用 find_namespace_packages 自動尋找專案 packages
    packages = find_namespace_packages(include=['template*']),
    # 使用 MANIFEST.in 設定要包含的資料檔
    include_package_data = True,
    # 設定該專案的依賴套件
    install_requires=[
        'pyyaml >= 6.0'
    ],
)
```

### 建立 MANIFEST.in
#### 說明
`MANIFEST.in` 是 setuptools 用於辨識要包含哪些檔案的文件，應該放置於 `setup.py` 同層級之目錄，撰寫方式可以參考 [官方文件][manifest]。

#### 設定檔
- manifest 提供了 8 種判斷邏輯供使用：
    - include/exclude：加入或移除符合格式之檔案。
    - recursive-include/recursive-exclude：加入或移除所有符合格式之資料夾下，所有符合格式的檔案。
    - global-include/global-exclude：加入或移除專案內所有符合格式之檔案。
    - graft/prune：加入或移除符合格式之資料夾下所有檔案。
- 基本撰寫方式，可以參考此設定檔。

```text
include README.md
include CHANGELOG.md
include *.sh
include .gitignore
global-include requirements.txt
graft docs
graft notes
graft tests
graft template/conf
graft template/log
```

### 製作分發檔
#### 說明
在建立好以上檔案後，確認資料夾結構有以下檔案，之後就可以開始建立分發檔。

```sh
.
├── MANIFEST.in
├── README.md
├── setup.py
└── template
```

#### 操作
- 使用指令建立。
    - 在專案根目錄執行 (`setup.py` 在的位置)。
```sh
>>> python -m build
>>> python -m build -s # 只產出 .tar
>>> python -m build -w # 只產出 .whl
```

- 完成後會在根目錄看到 `dist/` 與 `*.egg-info` 兩個由 setuptools 自動建立的資料夾。
    - `dist/`：分發相關檔案會建立於此資料夾內。
    - `*.egg-info`：專案資訊會建立於此資料夾內。

```sh
.
├── dist
│   ├── template-0.1.0-py3-none-any.whl
│   └── template-0.1.0.tar.gz
├── MANIFEST.in
├── README.md
├── setup.py
├── template.egg-info
│   ├── PKG-INFO
│   ├── SOURCES.txt
│   ├── dependency_links.txt
│   ├── requires.txt
│   └── top_level.txt
└── template
```

### 部署分發檔
#### 說明
若要部署於新環境，僅需攜帶 `.whl` 或 `.tar` 檔即可。

- `.whl` 為分發安裝檔，**僅包含 package 部分。**
- `.tar.gz`為完整原始檔，**包含完整專案資料夾 (文件、測試集等)。**

#### 操作
- 使用 pip 安裝。
```sh
# -t 可以指定安裝位置，此處指定為當前位置 (未指定預設會安裝至 python library)
>>> pip install template-0.1.0-py3-none-any.whl -t .
```

- 或是直接解壓縮 `.tar.gz` 到目標位置 (不建議)。
```sh
>>> tar -xzf template-0.1.0.tar.gz
```

## 補充
### 關於 Python Package Index (PyPI)
PyPI 是 Python 官方使用的第三方套件發佈平台，也是 `pip` 使用的套件來源。

透過 Setuptools 建立好的分發包也可以上傳至 PyPI 供其他開發者使用。


## 參考
- [pypi]
- [setuptools]
- [setup()][keywords]
- [manifest]

[pypi]: https://pypi.org/
[setuptools]: https://setuptools.pypa.io/en/latest/index.html
[keywords]: https://setuptools.pypa.io/en/latest/references/keywords.html
[manifest]: https://packaging.python.org/en/latest/guides/using-manifest-in/