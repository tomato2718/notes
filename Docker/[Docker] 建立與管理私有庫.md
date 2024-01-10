# [Docker] 建立與管理私有庫
## 概要
此篇文章介紹如何使用 Docker 建立與管理 private registry。

> **注意:** 此篇文章假設你已經安裝 Docker。

## 說明
### 環境準備
- 建立相關資料夾備用。

```sh
>>> mkdir docker
>>> cd docker
>>> mkdir registry
```

- 建立設定檔。
    - 參考 [官方文件]

```sh
>>> cat > ./config.yml <<EOF
version: 0.1
log:
  fields:
    service: registry
storage:
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
  delete:
    enabled: true # 開啟遠端刪除
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
EOF
```

### 啟動私有庫
- 建立啟動腳本

```sh
>>> cat > ./runRegistry.sh << EOF
docker run \
-d \
-p <ip>:<port>:5000 \
--name myRegistry \
-v /path/to/config.yml:/etc/docker/registry/config.yml \
-v /path/to/registry:/var/lib/registry \
--restart always \
registry
EOF
```

- 修改腳本執行權限

```sh
>>> chmod 744 ./runRegistry.sh
```

- 啟動 image

```sh
>>> ./runRegistry.sh
```

- 開啟防火牆設定

```sh
>>> firewall-cmd --permanent --add-port 5000/tcp
>>> firewall-cmd --reload
```

- 完成！

### 於本機進行操作
> **注意:** 推薦使用 VsCode 的 Docker 擴充套件

> **注意:** 假設 registry IP 為 `10.10.1.100:5000`

- 重新標記 image

```sh
>>> docker tag MyImage:1.0.0 10.10.1.100:5000/MyImage:1.0.0
```

- 將 image 推送至儲存庫
    - image 需要帶 registry IP

```sh
>>> docker push 10.10.1.100:5000/MyImage:1.0.0
```

- 連接 Docker 擴充套件
    - 連接後即可透過 UI 進行相關操作



[官方文件]: https://docs.docker.com/registry/configuration/