# OpenTelemetry Collector 基本設定
## 概要
在安裝好 collector 之後，需要進行設定才能開始運作。collector 由 `receivers`, `processors`, `exporters`, `extensions`, `service` 等五個元件組成，如下方提供之 `yaml` 範本，接下來會依序介紹各個元件。

參考：[官方文件][otelcol doc]

## 設定檔範本
```yml
# 宣告為 receiver 區塊
receivers:
  # 使用的 receiver，設定方式見各文章
  otlp:
    protocols:
      grpc:
        endpoint: <ip:port>
      http:
        endpoint: <ip:port>
# 宣告為 processor 區塊
processors:
  # 使用的 processor，設定方式見各文章
  memory_limiter:
    limit_mib: 400
    spike_limit_mib: 100
    check_interval: 5s
# 宣告為 exporter 區塊
exporters:
  # 使用的 exporter，設定方式見各文章
  otlp:
    endpoint: <ip:port>
# 宣告為 extension 區塊
extensions:
  # 使用的 extension，設定方式見各文章
  zpages: {}
  memory_ballast:
    size_mib: 165
# 宣告為 service 區塊
service:
  # 要導入的擴充套件，若各個元件有使用的話需要於此設定
  extensions: [zpages, memory_ballast]
  # 要執行的 pipelines
  pipelines:
    # 轉發 traces
    # 此處可自行命名，格式為 <type>/<name>
    # /<name> 部分可忽略不設定
    traces/1:
      # 該項目使用的 receivers
      receivers: [otlp]
      # 該項目使用的 processors
      # 未使用時可直接移除此標籤
      processors: [memory_limiter, batch]
      # 該項目使用的 exporters
      exporters: [otlp]
    # 轉發 metrics，設定方法與 traces 相同
    metrics/1:
      receivers: []
      exporters: []
    # 轉發 logs，設定方法與 traces 相同
    logs/1:
      receivers: []
      exporters: []
  # 該 collector 的遙測資料，一般不用設定
  # 詳細參考官方文件
  telemetry:
    metrics/1:
    logs/1:
```

## receivers
receivers 用於**接收**資料，透過監聽特定位址來取得其他來源寄送的資料。

官方版本 collector 僅提供 `otlp receiver` 以接收 `otlp` 來源為主，若需要從 `kafka`, `jaeger` 等其他來源接收資料，可以至社群版本 `otelcol-contrib` 的 [github repo][contrib-receivers repo] 下查看 (需要安裝社群版 `otelcol-contrib`)。

**注意**：並非所有 `receivers` 都支援 `traces`, `metrics`, `logs`。

## processors
processors 用於**處理**資料。

詳細待補充。

## exporters
exporters 用於**寄送**資料。

官方版本 collector 提供 `otlp` (grpc), `otlphttp`, `logging` 三種 exporter，若需要使用其他 exporter，可以至社群版本 `otelcol-contrib` 的 [github repo][contrib-exporters repo] 下查看 (需要安裝社群版 `otelcol-contrib`)。

**注意**：並非所有 `exporters` 都支援 `traces`, `metrics`, `logs`。

## extensions
除了基本功能以外，可能需要透過額外的套件來滿足客製化需求，此時加入擴充套件來達成目標。除了官方與社群提供的套件以外，也可以自行以 `golang` 編寫。

## service
service 用於**定義**寄送規則。

在定義好上述 4 種元件後，需要透過定義 service 來啟用功能，可以透過定義複數 service 來同時寄送遙測資料至不同目標。

service 有三個部分，分別為 `extensions`, `pipelines`, `telemetry`，其中 `pipelines` 為主要設定接收與寄送遙測資料的區塊。

參考 [官方文件 service 章節][service doc]。

## 參考
- [otelcol doc]
- [receivers repo]
- [contrib-receivers repo]
- [exporters repo]
- [contrib-exporters repo]
- [service doc]



<!-- otelcol -->
[otelcol doc]: https://opentelemetry.io/docs/collector/configuration/
[receivers repo]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/receiver
[contrib-receivers repo]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver
[exporters repo]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter
[contrib-exporters repo]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter
[service doc]: https://opentelemetry.io/docs/collector/configuration/#service
