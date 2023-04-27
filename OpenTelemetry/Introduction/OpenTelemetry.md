# OpenTelemetry
## 概要
### 關於 Open Telemetry
otel 是一款由 `OpenTracing` 與 `OpenCensus` 集合而成，用於採集遙測資料與部署分散式追蹤的工具。
otel 可以透過在 app 中以對應語言部署 SDK 來取得 traces, logs, metrics 等遙測資料，並透過 OpenTelemetry Protocol (otlp) 寄送至後端服務進行分析處理。

### 應用場景
otel 支援多種語言和平台，可以由 `Java`, `.NET` 等平台接收資料，透過 http request 的方式寄送至 kafka, otelcol 等中介服務，再 (或直接) 寄送至 Elastic APM, Grafana trace 等後端平台。

## 名詞解釋
### OpenTelemetry (otel)
otel 是這套工具的總稱，並不代表實際的工具。

### OpenTelemetry Protocol (otlp)
otlp 是遙測資料編碼與交換的協定，協定使用 `protoco buffer` 建構資料，並使用 `otlp/http` 或 `otlp/grpc` 進行編碼與傳輸。

### OpenTelemetry SDKs (otel sdk)
otel sdk 是用於部署於 app 中的開發套件，用於採集遙測資料。在多數主流語言中皆有實作，可以透過自動部署的方式快速導入監控，或是透過手動調用套件 API ，更進一步的採集數據。

### OpenTelemetry Collector (otelcol)
otelcol 是用於接收與轉發遙測資料的套件，可以透過 collector 統一處理各個來源的遙測資料。

collector 由五個部分構成，分別為：
- receivers：用於**接收**資料，透過監聽特定位址來取得其他來源寄送的資料。
- processors：用於**處理**資料。
- exporters：用於**寄送**資料，除了 otlp 以外也支援多種平台。
- extensions：新增在各個處理階段能使用的擴充套件，使用 `golang` 語言編寫。
- services：用於定義服務，在設定以上四者後須要於此建立服務才會實際運作。

### OpenTelemetry Agent (otel agent)
otel agent 本質上與 collector 相同，差別僅在使用方式上的不同。agent 是將 collector 部署於各個 host 上，讓 app 就近寄送資料，再由 agent 轉發至 collector。

### OpenTelemetry Collector Contrib (otelcol-contrib)
### OpenTelemetry Operator