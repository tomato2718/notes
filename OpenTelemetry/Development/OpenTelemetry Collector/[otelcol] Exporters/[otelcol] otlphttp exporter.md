# [otelcol] otlphttp exporter 設定方法
## 概要
`otlphttp exporter`，使用 `http` 寄出資料。能寄出 `traces`, `metrics`, `logs`(beta) 三種類型的資料。

詳細參考 [官方文件][otlphttp exporter]。

## 設定檔
```yml
# 宣告為 exporter 區塊
exporters:
  # 使用 otlp/http exporter
  # 此處可自行命名，格式為 <exporter>/<name>
  # /<name> 部分可忽略不設定
  otlphttp/MyOtlphttp01:
    # 要寄送至的目標
    # 須帶有 https:// 等 scheme
    endpoint: "https://otlp-gateway-prod-au-southeast-0.grafana.net/otlp"
    # 等待回應的時間上限 (單位為秒)
    timeout: 30
    # 認證方式，此處使用擴充套件
    # 詳細見 extensions 部分
    auth:
      authenticator: basicauth/otlp
```

## 補充
### 關於 TLS 證書
詳細參考 [這篇文章][otlphttp exporter tls]

## 參考
- [otlphttp exporter]
- [otlphttp exporter tls]

[otlphttp exporter]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/otlphttpexporter
[otlphttp exporter tls]: https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configtls/README.md