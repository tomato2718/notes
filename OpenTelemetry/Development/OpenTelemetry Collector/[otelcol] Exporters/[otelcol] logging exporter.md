# [otelcol] logging exporter 設定方法
## 概要
`logging exporter` 會將 collector 的資訊輸出至 console，並不會實際寄送至外部。

詳細參考 [官方文件][logging exporter]。

## 設定檔
```yml
# 宣告為 exporter 區塊
exporters:
  # 使用 logging exporter
  logging:
    # **已廢棄，與下者相互衝突**，記錄層級
    loglevel: info
    # 記錄層級 (detailed | normal | basic)
    verbosity: normal
    # 每秒紀錄數量
    sampling_initial: 1
```

## 參考
- [logging exporter]

[logging exporter]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/loggingexporter
