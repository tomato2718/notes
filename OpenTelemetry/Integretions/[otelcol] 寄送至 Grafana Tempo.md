# [otelcol] 寄送至 Grafana Enterprise Traces
## 概要
此篇文章介紹如何使用 OpenTelemetry Collector 寄送 traces 至 Grafana Enterprise Traces (GET)。

**注意**：此篇文章假設你已經了解 otelcol 基本設定方法。

## 環境準備
進行這篇文章的動作，會需要有已經準備好的：
- OpenTelemetry Collector
- 完成設定的 GET server
- GET server 提供的 Basic Auth 帳密

## 說明
### GET 部分
- 開啟 GET 設定檔
```sh
# 路徑視安裝環境可能不同
>>> vim /etc/enterprise-traces/enterprise-traces.yaml
```

- 於 receiver 區塊新增 otlp receiver
    - 建議使用 `otlp/grpc`
    - `otlp/http` 測試時有設定問題，尚未成功

```yml
# 詳細參考 otelcol 設定文章
distributor:
  receivers:
    otlp:
      protocols:
        grpc:
          # 監聽所有來源之 4318 port
          endpoint: 0.0.0.0:4318
```

- 開通防火牆
```sh
>>> firewall-cmd --permanent --add-port 4318/tcp
>>> firewall-cmd --reload
```

- 重啟 GET
```sh
>>> systemctl restart enterprise-traces
```

### Collector 部分
- 使用 base64 編碼 Basic Auth 帳密
    - 注意：是使用 tenant 名稱與 token，並非 api key
```sh
>>> echo -n <user name>:<tenant token> | base64 -w 0
ZGV2LXRlbmFudDpaR1YyTFhKbFlXUXR0000000000GNHOXNhV041TFhSbGMzUXRkRzl0000000000TZmRE5jUlMxNU16RmZOM0ZjTUNFNWV6UTROU3RzSlRjdA==
```

- 建立 otelcol exporter
```yml
    exporters:
      # 注意是使用 otlp，非 otlphttp
      otlp/GET:
        # GET 設定的 receiver
        # 假設上方 GET 位址為 10.10.1.100
        # 此處設定為 "10.10.1.100:4318"
        endpoint: "<ip>:<port>"
        # 內部網路未加密，因此需要關閉 tls
        tls:
          insecure: true
        headers:
          # 剛剛編碼好的帳密
          # 使用 Basic 認證
          Authorization: "Basic ZGV2LXRlbmFudDpaR1YyTFhKbFlXUXR0000000000GNHOXNhV041TFhSbGMzUXRkRzl0000000000TZmRE5jUlMxNU16RmZOM0ZjTUNFNWV6UTROU3RzSlRjdA=="
    service:
      pipelines:
        traces/1:
          receivers: [...]
          # 加上剛剛建立的 exporter
          exporters: [..., otlp/GET]
```

- 重啟/重建 otelcol 已套用設定
    - 方法視部署環境而定

### 完成
完成後可至 grafana 頁面 explore 下查看。

## 補充
### 關於 Grafana agent
官方文件提供的方法大多是使用 grafana agent 進行，grafana agent 本身是由 otelcol 為基底擴增 grafana 體系的功能，在收發遙測資料的部分與 otelcol 大致相同。

若已經在使用 otelcol 的話，可以直接由 otelcol 發送資料，沒必要寄送至 grafana agent。

## 參考
- https://grafana.com/blog/2022/04/26/set-up-and-observe-a-spring-boot-application-with-grafana-cloud-prometheus-and-opentelemetry/