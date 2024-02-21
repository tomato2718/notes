# Docker python 編譯環境建立

## 概要

- **注意**：根據 python 版本不同，安裝方法可能失效，目前僅測試 `python3.10.10`
- 此篇文章使用 `python:3.10.10-slim-bullseye` 建立環境，若要在其他系統建立編譯環境，參考 `安裝指定版本 Python`
- Dockerfile 說明：
    - `bullseye.nuitka`：使用官方 `python:3.10.10-slim-bullseye` 建立，安裝了 Nuitka 相關依賴

## 操作
### 安裝

- 導入或建立 image

```sh
# 由本地導入 image
docker load docker_nuitka.tar
# 由 docker file 建立 image
docker build -t nuitka -f Dockerfile.bullseye.tar .
```

- Dockerfile 參考

```Dockerfile
FROM python:3.10.10-slim-bullseye

WORKDIR /usr/src
RUN apt-get update \
    && apt-get install -y build-essential \
    && apt-get install -y ccache \
    && pip install nuitka
ENTRYPOINT [ "python" , "-m", "nuitka"]
CMD
```


## 使用
### 啟動

- 本機指定到要編譯的專案目錄

```sh
cd /path/to/my/project
```

- 使用 docker 編譯檔案
    - `-i`：執行時 container 會接收鍵盤輸入
    - `-t`：執行時 container 將畫面轉出給使用中終端
    - `--rm`：退出 container 後自動移除
    - `-v`：掛載資料夾 (host:container)
    - `--entrypoint`：由其他入口進入
    - `nuitka`：image 名稱

```sh
# 已將入口設定為 `python -m nuitka`
# 查看說明
docker run -it --rm nuitka -h

# 編譯 lib
docker run -it --rm -v `pwd`:/usr/src nuitka lib --module --no-pyi-file --remove-output --include-package=lib

# 由其他入口進入
docker run -it --entrypoint bash nuitka
```

## 其他
- Python 安裝檔下載：[點我][python]
- Docker Hub：[點我][docker hub]


[python]: https://www.python.org/ftp/python/
[docker hub]: https://hub.docker.com/_/python