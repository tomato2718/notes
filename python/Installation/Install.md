# 安裝指定版本 Python
## 概要
此篇文章說明如何安裝指定版本 python

## 環境資訊
### 作業系統
此篇文章以 Centos7, Debian 11 為例

### 需求
- python：[點我][python]
- openssl：[點我][openssl]
- gcc
- Make
- zlib
- libffi

### 操作
#### 安裝
- 安裝 gcc 與其他依賴
    - 若平台未提供類似工具，可從官網取得相關資源。
    - 注意：自行編譯可能會需要數個小時
- CentOS 7 repo 提供之 gcc 版本較舊，改用開發工具包代替
    - Devtoolset：[點我][devtoolset]
- Debain 11 repo 提供版本為 gcc 10

```sh
# CentOS 7
yum install centos-release-scl
yum install devtoolset-8
yum install zlib-devel
yum install libffi-devel
scl enable devtoolset-8 bash
# Debian 11
apt-get install build-essential
apt-get install libffi-dev
apt-get install zlib1g-dev
```

- 確認 gcc 版本

```sh
gcc --version
```

- 安裝 openssl
    - 非必要，但會造成無法使用 pip

```sh
# 解壓縮原始碼包
tar zxvf ./openssl-1.1.1t.tar.gz
# 進入資源資料夾
cd openssl-1.1.1t
# 編譯環境設定
./config --prefix=/<user dir>/openssl --openssldir=/<user dir>/openssl no-ssl2
# 編譯
make
# 測試編譯檔案
make test
# 安裝編譯檔案
make install
# 設定環境
export PATH=$HOME/openssl/bin:$PATH
export LD_LIBRARY_PATH=$HOME/openssl/lib
export LDFLAGS="-L /root/openssl/lib -Wl,-rpath,/root/openssl/lib"
```

- 安裝 python
    - 此處以 Python3.10.10 為例

```sh
# 解壓縮原始碼包
tar zxvf ./Python-3.10.10.tgz
# 進入資源資料夾
cd Python-3.10.10
# 編譯環境設定
./configure --enable-optimizations --with-openssl=/<usr dir>/openssl
# 編譯
make
# 測試編譯檔案
make test
# 安裝編譯檔案
make install
```

- 測試 python 是否正常安裝

```sh
python3 --version
```

- 完成！

[python]: https://www.python.org/ftp/python/
[openssl]: https://www.openssl.org/source/
[devtoolset]: https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/