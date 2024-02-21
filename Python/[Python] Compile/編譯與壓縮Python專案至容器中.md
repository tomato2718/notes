# 編譯與壓縮 Python 專案至容器中

## 概要

這幾天在研究編譯 python 專案並在 scratch 中執行，這邊做些筆記。

## DockerFile

```dockerfile
# 1. 建立編譯器
FROM python:3.11.8-slim-bullseye AS compiler

# 安裝 nuitka 需求
RUN apt update \
    && apt install -y build-essential ccache patchelf clang libfuse-dev upx \
    && pip install nuitka

# 2. 編譯與壓縮專案
FROM compiler AS compressor

WORKDIR /usr/src

# 將專案和依賴列表複製進 image
COPY ./<myapp> ./app
COPY ./requirements.txt ./

# 安裝依賴
RUN pip install --upgrade pip \
    && pip install --no-cache-dir -r requirements.txt

# 開始編譯
RUN python -m nuitka \
    --standalone \ # 編譯為獨立運作 binary
    --python-flag="-O,-m" \ # -m 為模組化執行、-O 為最佳化執行
    --remove-output \ # 移除編譯輸出
    --clang \ # 使用 clang 來編譯
    --prefer-source-code \ # 若原始碼可取得，使用原始碼編譯
    --include-package-data=limits \ # 如果一些 package 包含非 py 檔案，要手動導入
    app # 要編譯的目標

# 編譯完成會產生 app.dist 資料夾，當中 app.bin 是可執行檔
RUN cd app.dist \ # 移動到 app.dist 中方便操作
    && mkdir lib \ # 建立 lib 資料夾，方便後續把依賴檔案放進來
    # 將執行 binary 需要的程式庫複製至 lib. 中
    # 可以用 ldd app.bin 來查看依賴
    && cp \
    /lib/aarch64-linux-gnu/libdl.so.2 \
    /lib/aarch64-linux-gnu/libm.so.6 \
    /lib/aarch64-linux-gnu/libpthread.so.0 \
    /lib/aarch64-linux-gnu/libutil.so.1 \
    /lib/aarch64-linux-gnu/libgcc_s.so.1 \
    /lib/aarch64-linux-gnu/librt.so.1 \
    /lib/aarch64-linux-gnu/libc.so.6 \
    /lib/aarch64-linux-gnu/libz.so.1 \
    /lib/ld-linux-aarch64.so.1 \
    ./lib \
    # 把幾個編譯後的檔案從 app.dist/ 移動到 app.dist/lib/ 中
    # 我也不太確定原因，但不移動就會執行失敗
    && mv \
    libpython3.11.so.1.0 \
    libssl.so.1.1 \
    libcrypto.so.1.1 \
    ./lib \
    # 壓縮 app 主體
    && upx --best app.bin


# 因為已經把依賴庫都放到 app.dist 中了，所以可以直接從 scratch 建立分發用 image
FROM scratch

# 將 app.dist 作為根目錄，直接把所有內容複製到 / 
COPY --from=compressor /usr/src/app.dist/ /

# 大功告成！
ENTRYPOINT [ "/app.bin" ]
```

## 收尾(?)

最後當然要展示一下壓縮的成果，從原本使用 alpine 建立的鏡像 109MB 壓縮成 74.2MB，減少了 32% 的體積

如果能進一步整理 python package 中沒用到的 code，相信能再進一步壓縮，不過太花時間這邊就先不研究了 xd

```sh
REPOSITORY           TAG       IMAGE ID       CREATED          SIZE
compiled             latest    4a36786d8d4d   2 hours          74.2MB
original             0.9.1     f74f0962649a   27 hours ago     109MB
```