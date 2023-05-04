# [otelcol] 寄送至 Elastic APM
## 概要
此篇文章介紹如何使用 OpenTelemetry Collector 寄送 traces 至 Elastic APM。

**注意**：此篇文章假設你已經了解 otelcol 基本設定方法。

## 環境準備
進行這篇文章的動作，會需要有已經準備好的：
- OpenTelemetry Collector
- 完成設定的 Elastic fleet server
- ElasticSearch 提供的 APIKey

## 說明
### Collector 部分
- 建立 otelcol exporter
```yml
exporters:
  # 注意是使用 otlp，非 otlphttp
  otlp/Elastic:
    # 使用 Elastic APM 頁面提供的位址
    endpoint: "10.10.1.78:8200"
    # 若使用 http，需要關閉 tls
    tls:
      insecure: true
    # APIKey 使用 Bearer 認證
    headers:
      Authorization: "Bearer cERpMm1JY0J4VV9UNmRvYz0000000000VERYZEFSX1NzY1JBNDBERkZmUQ=="
service:
  pipelines:
    traces/1:
      receivers: [...]
      # 加上剛剛建立的 exporter
      exporters: [..., otlp/Elastic]
```

- 重啟/重建 otelcol 已套用設定
    - 方法視部署環境而定

### 完成
完成後可至 Elastic 頁面 APM 下查看。

## 參考
- [官方文件]

[官方文件]: https://www.elastic.co/guide/en/apm/guide/current/open-telemetry-direct.html