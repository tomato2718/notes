# Nuitka 簡易介紹與操作說明

## 概要

Nuitka 是用來將 `.py` 打包為 `.exe` 或 `.bin` 的第三方套件，此篇文章介紹 Nuitka 的環境準備與基本使用方法。

## python 熱門編譯工具的比較

### PyInstaller

- 相容性：python 3.6+
- 環境：直接安裝即可
- 方法：將檔案編譯為 `.pyc`
- 安全性：`.pyc` 容易被反編譯，需要另外配合 Cython 轉換

### Nuitka

- 相容性：python 2.6, 2.7, 3.3+
- 環境：須額外安裝環境對應編譯器
- 方法：將檔案編譯為 binary file
- 安全性：binary file 較難以反編譯

## Nuitka 環境準備
### 安裝 C Compiler

#### Macos
使用 clang。

- 安裝 llvm。
```sh
>>> brew install llvm
```

#### Linux
安裝 gcc 5.1+, g++ 4.4+。

- 可以安裝 [devtoolset][devtoolset] 套件。
- 或是參考 [gcc 官網][gcc]。

```sh
# Centos 安裝 devtoolset
# 安裝依賴套件
>>> yum install centos-release-scl
# 安裝 devtoolset 套件
>>> yum install devtoolset-8
# 啟用套件
>>> scl enable devtoolset-8 bash
# 確認 gcc 版本
>>> gcc --version
```

#### 全平台
安裝 ccache。

- 非必要，但可以加快打包速度

```sh
>>> yum install ccache
```

### 安裝 Nuitka

- 安裝 nuitka。

```sh
# 安裝
>>> pip install nuitka
# 確認版本
>>> python -m nuitka --version
```

- 安裝 python-devel。

```sh
>>> yum install python3-devel
```


### Nuitka 操作
#### 基本

- 呼叫 nuitka：

```sh
# 呼叫 nuitka 打包指定檔案
python -m nuitka <target>
```

- 常用指令：

```sh
# 顯示說明
-h, --help

# 選擇用來打包的 python 版本
# 僅支援 2.6, 2.7, 3.3+
# 預設為呼叫 nuitka 時使用之版本
--python-for-scons=<path to python.bin>

# 指定打包後檔案名稱
-o <name>, --output-filename=<name>

# 結束後移除打包過程中產生的檔案
--remove-output 

# 指定執行時之 python 參數
# e.g. python -o main.py, python -m nuitka.py
--python-flag=<flag>

# 使用 clang 來編譯
--clang
```

- 追蹤 import：

```sh
# 追蹤 python 標準庫中所有 import
--follow-stdlib

# 追蹤所有使用到的 import
--follow-imports

# 不追蹤任何 import
--no-follow-imports

# 追蹤指定目標的 import
--follow-import-to=<target>

# 不追蹤指定目標的 import
--no-follow-import-to=<target>
```

- 打包選項：

```sh
# 打包為不依賴 python 執行之 binary
--standalone

# 打包為單一檔案
# 不建議使用 onefile 模式
--onefile

# 打包為 python package
--module

```
- 指定包含的內容
    - 通常在 nuitka 沒有發現 import 時手動指定

```sh
# 打包目標 package
--include-package=<name>

# 打包目標 module
--include-module=<name>

# 打包目標資料夾中的非 .py 檔案
--include-data-files=<目標絕對路徑>=<專案內相對路徑>
--include-data-dir=<目標絕對路徑>=<專案內相對路徑>
```

### 更多指令
- 參考
    - [官方文件][nuitka]
    - `-h`, `--help` 指令


## 參考
- [官方文件][nuitka]

[devtoolset]: https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/
[gcc]: https://gcc.gnu.org/mirrors.html
[nuitka]: https://nuitka.net/doc/user-manual.html#