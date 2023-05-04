# [otelcol-contrib] 寄送至 grafana cloud - tempo
## 概要
此篇文章介紹如何使用 OpenTelemetry 寄送 traces 至 grafana cloud - tempo。

**注意**：此篇文章假設你已經了解 otelcol 基本設定方法。

**另一個注意**：Grafana Cloud 使用 `https` 與 `Basic Authentication`，因此需要安裝社群版 collector。

## 環境準備
- OpenTelemetry Collecto Contrib
- Grafana Cloud

## 說明
### Grafana Cloud 部分
- 前往 grafana 個人頁面 (不是 grafana cloud)，點選 grafana cloud details
![img01]

- 記下 `Instance ID` 與 `Zone` 區塊的資訊

![img02]

- 前往左方 API Key 頁面，建立 API Key
    - 需要相關權限才能前往該頁面
    - role 選擇 `MetricsPublisher`

![img03]

### Collector 部分
- 於 otelcol 建立對應區塊
```yml
# 宣告 extension 區塊
extensions:
  # 建立 basicauth 擴充
  basicauth/otlp:
    # 使用 client 認證
    client_auth:
      # 帳號為剛剛紀錄的 Instance ID
      username: <Instance ID>
      # 密碼為 APIKey
      password: <password>
# 宣告 exporter 區塊
exporter:
  # 建立 exporter
  otlphttp/grafanaCloud:
    # grafana cloud 位址為：https://otlp-gateway-<zone>.grafana.net/otlp
    # zone 為剛剛紀錄的 Zone
    endpoint: "https://otlp-gateway-prod-au-southeast-0.grafana.net/otlp"
    # 宣告於此 exporter 使用剛剛建立的擴充套件
    auth:
      authenticator: basicauth/otlp
service:
  # 記得要於 service 處導入擴充套件
  extensions: [..., basicauth/otlp]
  pipelines:
    traces/1:
      receivers: [otlp]
      # 設定使用剛剛建立的 exporter
      exporters: [..., otlp/a41-grafana]
```

## 補充
### 關於 APIKey
Grafana Cloud 的 APIKey 是由 Grafana 頁面建立，並非 cloud 內的 APIKey 或 access policy。

此處 APIKey 是做為 `Basic Authentication` 之密碼使用，並非 `Bearer` 之 APIKey。

### 關於 BasicAuth 擴充套件
該套件是用於快速設定 `Basic Authentication`，詳情請見介紹文章。


## 參考
- https://grafana.com/blog/2022/04/26/set-up-and-observe-a-spring-boot-application-with-grafana-cloud-prometheus-and-opentelemetry/

[img01]: ./img/grafana_cloud_01.png
[img02]: ./img/grafana_cloud_02.png
[img03]: ./img/grafana_cloud_03.png