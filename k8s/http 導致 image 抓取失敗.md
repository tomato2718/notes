# http 導致 image 抓取失敗
## 環境資訊
- `kubernets v1.27.0`
- `docker 23.0.3`
- `containerd 1.6.20`

## 問題描述
### 錯誤資訊
- 已使用 docker 在 `control plane` 建立 private registry
- k8s 的 worker node 向該 registry 抓取 image 時顯示 `ImagePullBackOff` ，如下
```sh
>>> kubectl get po
NAME                              READY   STATUS             RESTARTS        AGE
api-deployment-cf6b458f8-scjs2    0/1     ImagePullBackOff   0               35m

>>> kubectl describe po api-deployment-cf6b458f8-scjs2 
Failed to pull image "10.10.1.106:5000/apiserver:0418": rpc error: code = Unknown desc = failed to pull and unpack image "10.10.1.106:5000/apiserver:0418": failed to resolve reference "10.10.1.106:5000/apiserver:0418": failed to do request: Head "https://10.10.1.106:5000/v2/apiserver/manifests/0418": http: server gave HTTP response to HTTPS client
``` 
### 問題發生原因
`containerd` 預設是使用 `https` 進行連線，但架設的 registry 使用 `http` 連線

## 解決方法
### 改為使用 https 架設 registry
#### 描述
- 修改對象為 **control plane** 

#### 操作
待補充。

### 修改 docker 使用 http 連線
#### 描述
- 舊版本的 k8s 可以使用此方法排除問題
- 修改對象為 **worker node** 
- 新版本 k8s 已改為直接調用 containerd，請參考下一節方法

#### 操作
- 修改 docker 設定檔
```sh
>>> vim /etc/docker/daemon.json
```
```json
{
    "insecure-registries": ["<registry address>: <port>"]
}
```
- 重啟 docker
```sh
>>> systemctl restart docker
```

### 修改 containerd http 連線
#### 描述
- 新版本 k8s 使用此方法設定
- 修改對象為 **worker node** 

#### 操作
- 移動至 `containerd` 設定檔目錄
```sh
>>> cd /etc/containerd
```

- 導出 containerd 設定檔
```sh
>>> containerd config default > config.toml

>>> ls 
config.toml
```

- 對設定檔進行以下修改
```sh
>>> vim config.toml
```
```toml
...

[plugins]

  ### 新增此區塊 ###
  [plugins."io.containerd.grpc.v1.cri".registry]
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors."<registry address>: <port>"]
        endpoint = ["<registry address>: <port>"]
    [plugins."io.containerd.grpc.v1.cri".registry.configs]
      [plugins."io.containerd.grpc.v1.cri".registry.configs."<registry address>: <port>".tls]
        insecure_skip_verify = true
  ### 區塊結尾 ###

  [plugins."io.containerd.gc.v1.scheduler"]
    deletion_threshold = 0
    mutation_threshold = 100
    pause_threshold = 0.02
    schedule_delay = "0s"
    startup_delay = "100ms"

...

    ### 移動到下方在重複的區塊加上註解 (或刪除) ###
    # [plugins."io.containerd.grpc.v1.cri".registry]
      # config_path = ""

      [plugins."io.containerd.grpc.v1.cri".registry.auths]

      # [plugins."io.containerd.grpc.v1.cri".registry.configs]

      [plugins."io.containerd.grpc.v1.cri".registry.headers]

      # [plugins."io.containerd.grpc.v1.cri".registry.mirrors]

...
```

- 重啟 `containerd`
```sh
>>> systemctl restart containerd
```

- 檢查設定是否成功
```sh
>>> containerd config dump
...
[plugins."io.containerd.grpc.v1.cri".registry]
      config_path = ""

      [plugins."io.containerd.grpc.v1.cri".registry.auths]

      [plugins."io.containerd.grpc.v1.cri".registry.configs]

        [plugins."io.containerd.grpc.v1.cri".registry.configs."10.10.1.106:5000"]

          [plugins."io.containerd.grpc.v1.cri".registry.configs."10.10.1.106:5000".tls]
            ca_file = ""
            cert_file = ""
            insecure_skip_verify = true
            key_file = ""

      [plugins."io.containerd.grpc.v1.cri".registry.headers]

      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]

        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."10.10.1.106:5000"]
          endpoint = ["http://10.10.1.106:5000"]
...
```

- 於 control plane 確認是否成功運作
```sh
>>> kubectl get po
NAME                             READY   STATUS    RESTARTS        AGE
api-deployment-cf6b458f8-scjs2   1/1     Running   0               69m
```