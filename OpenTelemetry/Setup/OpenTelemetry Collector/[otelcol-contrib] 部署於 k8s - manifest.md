# 在 k8s 上部署 OpenTelemetry Collector Contrib - 使用 Manifest
## 概要
`otelcol contrib` 的部署方式與 `otelcol` 相同，僅需要調整少數設定即可完成部署，此篇文章會介紹如何使用 `manifest` 進行部署。

**注意**：此篇文章假設你已經知道如何使用 manifest 部署 otelcol。

## 部署方法
### 修改 manifest
- 參考 otelcol 的文章，取得 manifest 之進行部分修改即可

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: otel-collector
  labels:
    app: opentelemetry
    component: otel-collector
spec:
  selector:
    matchLabels:
      app: opentelemetry
      component: otel-collector
  minReadySeconds: 5
  progressDeadlineSeconds: 120
  replicas: 1
  template:
    metadata:
      labels:
        app: opentelemetry
        component: otel-collector
    spec:
      containers:
      - command:
          - "/otelcol-contrib" # 這裡修改為 /otelcol-contrib
          - "--config=/conf/otel-collector-config.yaml"
        image: otel/opentelemetry-collector-contrib:0.75.0 # image 也修改為 opentelemetry-collector-contrib
...
```

### 部署
其餘部分皆與 otelcol 相同，請參考 otelcol 安裝方法。


## 補充
範例使用的為 0.75 版本 manifest，與最新版本可能有差異。

## 參考
- [社群版本使用範例]
- [docker hub]

[社群版本使用範例]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/examples/kubernetes
[docker hub]: https://hub.docker.com/r/otel/opentelemetry-collector-contrib/tags