# [Python] 用 Sphinx 建立 API 文件
## 概要
Sphinx 是一個用於建立 Python API 文件的工具，可以自動抽出專案中符合格式的 docstring 並建立為 html 頁面，社群中大多數的 API 文件也都是由此套件建立。

**注意**：此篇文章假設你已經了解什麼是 reStructuredText。

## 說明
### 安裝 sphinx 與擴充套件
#### 說明
直接使用 pip 安裝即可。

#### 操作
- 使用 pip 安裝套件主體。
```sh
>>> pip install sphinx
```
- (可選) 安裝擴充套件。
```sh
# Python 官方文件主題
>>> pip install python-docs-theme 
# 對 mermaid 的支援
>>> pip install sphinxcontrib-mermaid 
```

### 建立 sphinx 專案
#### 說明
使用 sphinx 會建立許多檔案，因此建議在獨立的資料夾中建立 sphinx 專案。

#### 操作
- 建立 api 文件資料夾
```sh
# 名稱可自訂
>>> mkdir docs/api_doc
```

- 建立 sphinx 專案
    - 建立時套件會詢問一些問題
```sh
# 使用 quickstart 快速建立專案框架
>>> sphinx-quickstart docs/api_doc --ext-autodoc
Welcome to the Sphinx 6.1.3 quickstart utility.

Please enter values for the following settings (just press Enter to
accept a default value, if one is given in brackets).

Selected root path: docs/api_doc

You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
# 是否分離原始碼與文件，建議選擇 y
> Separate source and build directories (y/n) [n]: y

The project name will occur in several places in the built documentation.
# 專案資訊，可以先亂填，稍後由設定檔修改
> Project name: template
> Author name(s): foo
> Project release []: 1.0.0
```

- 根據專案產生設定檔
```sh
# -o 指定輸出位置；位置參數指定要建立文件的模組路徑
>>> sphinx-apidoc -o docs/api_doc/source MyApp/lib
```

- 完成後資料夾應該會有以下結構
```sh
docs/api_doc
├── Makefile # macOS, linux 用
├── make.bat # Windows 用
├── build
│   ├── doctrees
│   └── html
└── source
    ├── _static
    ├── _templates
    ├── conf.py
    ├── index.rst
    ├── lib.rst
    └── modules.rst
```

### 撰寫 sphinx 專案
#### 說明
sphinx 專案由 reStructuredText 格式撰寫，可以把 sphinx 理解為 reStructuredText 的擴充套件。像是 typora 等編輯器將 Markdown 轉換為 html 格式，sphinx 則是將 reStructuredText 轉換為 html 格式，再根據自己的解析規則將特定識別符號轉為指定樣式並套入網頁框架中。

#### 操作
- 修改 `conf.py`
```py
### 要自己加入目標路徑 ###
import os, sys
sys.path.insert(0, os.path.abspath('../../../template'))
### 到這邊為止上面兩行 ###

# Configuration file for the Sphinx documentation builder.
#
# For the full list of built-in configuration values, see the documentation:
# https://www.sphinx-doc.org/en/master/usage/configuration.html

# -- Project information -----------------------------------------------------
# https://www.sphinx-doc.org/en/master/usage/configuration.html#project-information

### 專案資訊區塊 ###
# quick-start 建立時詢問的資訊會記錄在這裡，也可以直接修改
project = 'template'
copyright = '2023, foo'
author = 'bar'
release = '1.0.0'

# -- General configuration ---------------------------------------------------
# https://www.sphinx-doc.org/en/master/usage/configuration.html#general-configuration

### 擴充套件區塊 ###
# 加入 autodoc 和 mermaid 兩套件
extensions = ['sphinx.ext.autodoc', 'sphinxcontrib.mermaid']
# autodoc 的設定
autodoc_default_options = {"members": True, "undoc-members": True, "private-members": True}
autoclass_content = 'both'
# mermaid 的設定
mermaid_init_js = "mermaid.initialize({startOnLoad:true, theme:'neutral'});"

templates_path = ['_templates']
exclude_patterns = []

# -- Options for HTML output -------------------------------------------------
# https://www.sphinx-doc.org/en/master/usage/configuration.html#options-for-html-output

# 這邊可以改喜歡的文件主題
# 參考：https://www.sphinx-doc.org/en/master/usage/theming.html
# 推薦使用稍早安裝的 python_docs_theme
html_theme = 'python_docs_theme'
html_static_path = ['_static']
```

- 修改 `index.rst`
    - 這份文件就是 API 網頁的首頁
    - 如下區塊，加上 `modules` (上下需空行) 即可將 `apidoc` 產生的檔案連結至首頁
```rst
.. template documentation master file, created by
   sphinx-quickstart on Thu May 11 16:18:47 2023.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to template's documentation!
====================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   modules


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

- 建立文件
    - 使用 make 指令即可建立文件
    - 用 `-C` 指定 `Makefile` 的位置
```sh
>>> make html -C docs/api_doc
```

- 完成！
    - 完成後相關檔案會存放在 `docs/apidoc/build/html/` 之下
    - 用瀏覽器開啟 `index.html` 即可看到產生的文件

## 補充
### 若要加入其他頁面
sphinx 的每個頁面其實就是一份 rst 文件，如果要加入其他頁面，用 rst 格式寫好後照上方格式在 toctree 下加入該頁面的檔案名稱 (不用副檔名) 即可
### 若要自行建立文件
除了自動建立文件以外，sphinx 也提供了 API 讓使用者可以透過特定標籤自行建立想要的文件內容，使用方法參考 [官方文件頁面][API]。



## 參考
- [sphinx]
- [文件 API][API]

[sphinx]:  https://www.sphinx-doc.org/en/master/index.html
[API]: https://www.sphinx-doc.org/en/master/usage/restructuredtext/domains.html