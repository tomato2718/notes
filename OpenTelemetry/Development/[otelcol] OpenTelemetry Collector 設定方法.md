# OpenTelemetry Collector 設定方法
## 概要
在安裝好 `otelcol` 之後，需要進行設定才能使 `otelcol` 開始運作。安裝方法可以參考各安裝流程 [k8s][k8s install] 。

## 架構
collector 由五個部分構成，分別為：
- receivers：用於**接收**資料，透過監聽特定位址來取得其他來源寄送的資料。
- processors：用於**處理**資料。
- exporters：用於**寄送**資料，除了 otlp 以外也支援多種平台。
- extensions：新增在各個處理階段能使用的擴充套件，使用 `golang` 語言編寫。
- services：用於定義服務，在設定以上四者後須要於此建立服務才會實際運作。


## receivers
### 說明
`otelcol` 以接收 `otlp` 來源為主，若需要使用 `kafka`, `jaeger` 等其他來源 ，可以至 `otelcol-contrib` 的 [github repo][contrib-receivers] 下查看 (需要安裝社群版 `otelcol-contrib`)。

**注意**：並非所有 `receivers` 都支援 `traces`, `metrics`, `logs`

### otlp receiver
#### 說明
`otlp receivers` 可以接收任何使用 `otlp` 的來源，並且能接收 `traces`, `metrics`, `logs` 所有類型的遙測資料。

`otlp receivers` 提供 `grpc` 與 `http` 兩種協定，須注意 `exporter` 的 `otlp` 是對應 `grpc`，而 `otlphttp` 是對應 `http`，若使用錯誤的協定監聽會導致遙測寄送失敗。

#### 設定檔
```yml
########################
# 對外開放時記得開通防火牆 #
########################

# 宣告為 receivers 區塊
receivers:
  # 使用 otlp receivers
  # 此處可自行命名，格式為 <receiver>/<name>
  # /<name> 部分可忽略不設定
  otlp/MyReceiver1:
    # 指定協定，有 grpc 與 http 可選擇
    # 若只使用 grpc ，則不需要設定 http (直接移除該區塊)，反之同理
    protocols:
      # 設定 otlp/grpc 監聽
      grpc:
        # 監聽來源，預設 (建議) 使用 4317 port 
        # 部署於容器中：
        #   - 監聽 0.0.0.0 (所有來源)
        # 部署於實體機：
        #   - 監聽 localhost (只能接收本機來源，外部無法寄送)
        #   - 監聽該主機的 ip ，如 10.10.1.106 (能訪問該位址的機器都能寄送)
        endpoint: "0.0.0.0:4317"
      # 設定 otlp/http 監聽
      http:
        # 監聽來源，預設 (建議) 使用 4318 port 
        # 規則與 grpc 相同
        endpoint: "0.0.0.0:4318"
        # 跨來源資源共用，若資料來自不同網域則需要進行此設定
        # 相同網域則可以忽略
        # 參考：https://developer.mozilla.org/zh-TW/docs/Web/HTTP/CORS
        cors:
          # 接受的來源
          allowed_origins:
            # 接受所有來源的請求
            - "*"
            # 接受所有來自 foo.com 網域之下的請求
            - "http://*.foo.com"
          # 接受的 http headers
          # safelist 中的 http header 不需要再次設定
          allowed_headers:
            - "MyHeader"
          # preflight 結果保留的時間 (單位為秒)
          # 通常不用進行設定
          max_age: 600
```

### 補充
#### 關於 CORS
`CORS` 是 `HTTP` 中，限制不同網域的來源存取資源的機制，透過程式碼發出的請求都會被 `CORS` 阻擋下來，需要透過設定 `Access-Control-Allow-Origin` 來指定能接受的網域。

該協定的詳細可以參考 [這篇文章][CORS]

#### 關於 http headers
一個 http 請求會攜帶一些 headers 供 host 端辨識，除了基本的 safelist headers 以外，未透過 `Access-Control-Allow-Headers` 設定的 headers 都會被 CORS 阻擋下來。

safelist 可以參考 [這篇文章][CORS-safelist]

#### 關於 preflight
在特定情況下，client 會在發出實際請求前先發出預檢 (`preflight`)，詢問 host 是否接受該請求，多數情況會由瀏覽器或程式自動執行，不用自行設定。

---

## processors
### 說明
### 設定檔
### 補充
待更新。

---

## exporters
### 說明
`otelcol` 支援 `otlp` (grpc), `otlphttp`, `logging` 三種 `exporter`，若需要使用其他 `exporter`，可以至 `otelcol-contrib` 的 [github repo][contrib-exporters] 下查看 (需要安裝社群版 `otelcol-contrib`)。

**注意**：並非所有 `exporters` 都支援 `traces`, `metrics`, `logs`

### otlp exporter
#### 說明
`otlp exporter` 其實是 `otlp/grpc exporter`，使用 `grpc` 寄出資料。能寄出 `traces`, `metrics`, `logs` 三種類型的資料。

詳細參考 [官方文件][otlpgrpc exporter]。

#### 設定檔
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

### otlphttp exporter
#### 說明
`otlphttp exporter`，使用 `http` 寄出資料。能寄出 `traces`, `metrics`, `logs`(beta) 三種類型的資料。

詳細參考 [官方文件][otlphttp exporter]。

#### 設定檔
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

### logging exporter
#### 說明
`logging exporter` 會將 collector 的資訊輸出至 console，並不會實際寄送至外部。

詳細參考 [官方文件][logging exporter]。

#### 設定檔
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

### 補充
#### 關於 Authorization
比較常見的 `Authorization` 有兩種，分別為 `Basic` 與 `Bearer`。
- `Basic`：使用 `Base64` 編碼的 API key 認證。
- `Bearer`：使用 `Base64` 編碼的帳號密碼 `<user>:<password>` 認證。

詳細參考 [這篇文章][Authorization]

---

## extensions
### 說明
除了基本功能以外，可能需要透過額外的套件來滿足客製化需求，此時加入擴充套件來達成目標。除了官方與社群提供的套件以外，也可以自行以 `golang` 編寫。

由於並非核心功能，就不在此篇文章介紹。

---

## service
### 說明
在定義好上述 4 種元件後，需要透過定義 `service` 來啟用功能，可以透過定義複數 `service` 來同時寄送遙測資料至不同目標。

`service` 大致有三個部分，分別為 `extensions`, `pipelines`, `telemetry`，其中 `pipelines` 為主要設定接收與寄送遙測資料的區塊。

參考 [官方文件][service]

### 設定檔
```yml
# 宣告為 service 區塊
service:
  # 要導入的擴充套件，若各個元件有使用的話需要於此設定
  extensions: [zpages, memory_ballast, basicauth/otlp]
  # 要執行的 pipelines
  pipelines:
    # 轉發 traces
    # 此處可自行命名，格式為 <type>/<name>
    # /<name> 部分可忽略不設定
    traces/1:
      # 該項目使用的 receivers
      receivers: [otlp/MyReceiver1]
      # 該項目使用的 processors
      # 未使用時可直接移除此標籤
      processors: []
      # 該項目使用的 exporters
      exporters: [otlphttp/MyOtlphttp01, otlp/MyOtlp01]
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

---

## 參考
### otelcol
- [otelcol]
- [receivers]
    - [grpc receiver]
- [contrib-receivers]
- [exporters]
    - [otlpgrpc exporter]
        - [otlpgrpc exporter tls]
    - [otlphttp exporter]
    - [logging exporter]
- [contrib-exporters]
- [service]

### http
- [CORS]
- [CORS-safelist]
- [Authorization]
### misc
- [k8s install]

<!-- otelcol -->
[otelcol]: https://opentelemetry.io/docs/collector/configuration/
[receivers]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/receiver
[contrib-receivers]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver
[grpc receiver]: https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configgrpc/README.md

[exporters]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter
[contrib-exporters]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter
[otlpgrpc exporter]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/otlpexporter
[otlpgrpc exporter tls]: https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configtls/README.md
[otlphttp exporter]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/otlphttpexporter
[logging exporter]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/exporter/loggingexporter

[service]: https://opentelemetry.io/docs/collector/configuration/#service

<!-- HTTP -->
[CORS]: https://developer.mozilla.org/zh-TW/docs/Web/HTTP/CORS
[CORS-safelist]: https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header
[Authorization]: https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Authentication

<!-- other -->
[k8s install]: ../Setup/OpenTelemetry%20Collector/%5Botelcol%5D%20%E9%83%A8%E7%BD%B2%E6%96%BC%20k8s%20-%20manifest.md