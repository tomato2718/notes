# otlp/http 與 otlp/grpc 介紹
## 概要
otlp 是遙測資料編碼與交換的協定，在 otlp 之下有 `otlp/http` 與 `otlp/grpc` 兩種實作。

## otlp/http
### 訊息架構
`otlp/http` 與 `otlp/grpc` 皆採用 `protocol buffer` (`protobuf`) 做為遙測資料的架構，該架構的詳細可以參考 [protobuf 官方文件][protobuf]。

### 處理方式
`otlp/http` 提供兩種編碼方式，分別為 `binary` (`protobuf`) 與 `json`，多數 exporter 的預設皆使用 `binary` 編碼。

### 傳輸方式
`otlp/http` 同時支援 `HTTP/1.1` 與 `HTTP/2`。未特別指定的情況下，otel 相關工具會使用 `HTTP/1.1` 發出請求。

## otlp/grpc
### 訊息架構
`otlp/http` 與 `otlp/grpc` 皆採用 `protocol buffer` 做為遙測資料的架構，該架構的詳細可以參考 [protobuf 官方文件][protobuf]。

### 處理方式
`otlp/grpc` 使用 `gRPC` 框架進行編碼與發送，細節可以參考 [gRPC 文件]。

### 傳輸方式
`otlp/grpc` 只採用 `HTTP/2` 發出請求。

## 參考
- [官方文件]
- [protobuf]

[gRPC 文件]: https://grpc.io/docs/what-is-grpc/introduction/
[protobuf]: https://protobuf.dev/overview/
[官方文件]: https://opentelemetry.io/docs/reference/specification/protocol/otlp/