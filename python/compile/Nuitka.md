## Nuitka 簡易介紹與操作說明
### 概要
Nuitka 是用來將 `.py` 打包為 `.exe` 或 `.bin` 的第三方套件，此篇文章介紹 Nuitka 的環境準備與基本使用方法

### python 熱門編譯工具的比較

#### PyInstaller
相容性：python 3.6+
環境：直接安裝即可
方法：將檔案編譯為 `.pyc`
安全性：`.pyc` 容易被反編譯，需要另外配合 Cython 轉換

#### Nuitka
相容性：python 2.6, 2.7, 3.3+
環境：須額外安裝環境對應編譯器
方法：將檔案編譯為 binary file
安全性：binary file 較難以反編譯

### Nuitka 環境準備

#### 安裝 C Compiler

- Macos: clang
    - 安裝 llvm

```sh
brew install llvm
```

- Linux: gcc 5.1+, g++ 4.4+
    - 可以安裝 [devtoolset][devtoolset] 套件
    - 或是參考 [gcc 官網][gcc]

```sh
# Centos 安裝 devtoolset
# 安裝依賴套件
yum install centos-release-scl
# 安裝 devtoolset 套件
yum install devtoolset-8
# 啟用套件
scl enable devtoolset-8 bash
# 確認 gcc 版本
gcc --version
```

- 全平台: ccache
    - 非必要，但可以加快打包速度

```sh
yum install ccache
```

### 安裝 Nuitka

- 安裝 nuitla

```sh
# 安裝
pip install nuitka
# 確認版本
python -m nuitka --version
```

- 安裝 python-devel

```sh
yum install python3-devel
```

- 安裝 ordered-set
    - 非必要，但可以加快打包速度

```sh
pip install ordered-set
```

### Nuitka 操作

#### 基本

- 呼叫 nuitka
```sh
# 呼叫 nuitka 打包指定檔案
python -m nuitka <main.py>
```

- 常用指令

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
```

- import 相關
    - 必填
```sh
# 打包 python 標準庫
# 非必要不建議
--follow-stdlib

# 打包所有使用到的 import
--follow-imports

# 不打包任何 import
--no-follow-imports

# 打包指定 import
# 未用此指令打包之項目，必須存在專案資料夾或 python library 內
# 可以重複呼叫
--follow-import-to <target>

# 不打包指定 import
# 可以先 --follow-imports 全選，再用此指令移除指定目標
--no-follow-import-to <target>
```

- 打包為單一檔案
    - 檔案會相當龐大，非必要不建議使用

```sh
# 打包為不依賴 python 執行之單一獨立檔案
--standalone

# 打包為單一檔案，但還是依賴 python 環境
# 需要安裝 patchelf (linux) 與 zstandard (python)
# 須搭配 --include 來明確指定路徑
--onefile

# onefile 模式下必填，缺少任何檔案都會造成打包後檔案無法運作
# 支援 *
--include-data-files=<目標絕對路徑>=<專案內相對路徑>
--include-data-dir=<目標絕對路徑>=<專案內相對路徑>
```

- 打包 module
    - 可以打包工具類型的模組
    - `--include` 為必填項目，否則會打包空文件
```sh
# 打包 package，須配合 --include 打包不同類型目標
--module

# 打包 package (整個資料夾)
--include-package <name>

# 打包 module (單一 .py)
--include-module <name>
```

#### 範例

- 打包 main 與 lib，不打包第三方套件
```sh
python -m nuitka --python-flag=o --remove-output --follow-import-to lib -o myproject.bin main.py
```

- 打包所有 import
```sh
python -m nuitka --python-flag=o --remove-output --follow-imports -o myproject.bin main.py
```

#### 更多指令
- 參考
    - [官方文件][nuitka]
    - `-h`, `--help` 指令


### 參考
- 官方說明文件
    - [官方文件][nuitka]

[devtoolset]: https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/
[gcc]: https://gcc.gnu.org/mirrors.html
[nuitka]: https://nuitka.net/doc/user-manual.html#