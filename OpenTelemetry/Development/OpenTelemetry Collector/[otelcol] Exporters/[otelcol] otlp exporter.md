# [otelcol] otlp exporter 設定方法 (otlpgrpc)
## 概要
`otlp exporter` 其實是 `otlp/grpc exporter`，使用 `grpc` 寄出資料。能寄出 `traces`, `metrics`, `logs` 三種類型的資料。

詳細參考 [官方文件][otlpgrpc exporter]。

## 設定檔
```yml
# 宣告為 exporter 區塊
exporters:
  # 使用 otlp/grpc exporter
  # 此處可自行命名，格式為 <exporter>/<name>
  # /<name> 部分可忽略不設定
  otlp/MyOtlp01:
    # 要寄送至的目標，不用帶 https:// 等 scheme
    endpoint: "10.10.1.78:8200"
    # tls 設定
    tls:
      # 目標使用 http 時，須將 insecure 設定為 true
      insecure: true
      # 證書設定，**與 insecure 互相衝突**
      # 參考：https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configtls/README.md
      cert_file: file.cert
      key_file: file.key
    # 發出的 http 請求攜帶之 headers
    # 多數情況用於驗證 client
    headers:
      # 於 header 加入驗證區塊
      # 設定方式見各後端平台提供之 API
      Authorization: "Bearer <token>"
```

## 補充
### 關於 Authorization
比較常見的 `Authorization` 有兩種，分別為 `Basic` 與 `Bearer`。
- `Basic`：使用 `Base64` 編碼的 API key 認證。
- `Bearer`：使用 `Base64` 編碼的帳號密碼 `<user>:<password>` 認證。

詳細參考 [這篇文章][Authorization]

### 關於 TLS 證書
詳細參考 [這篇文章][otlpgrpc exporter tls]

## 參考
- [otlpgrpc exporter]
- [otlpgrpc exporter tls]
- [Authorization]

[otlpgrpc exporter]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/otlpexporter
[otlpgrpc exporter tls]: https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configtls/README.md
[Authorization]: https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Authentication