# [otelcol] otlp receiver 設定方法
## 概要
`otlp receivers` 可以接收任何使用 `otlp` 的來源，並且能接收 `traces`, `metrics`, `logs` 所有類型的遙測資料。

`otlp receivers` 提供 `grpc` 與 `http` 兩種協定，須注意 `exporter` 的 `otlp` 是對應 `grpc`，而 `otlphttp` 是對應 `http`，若使用錯誤的協定監聽會導致遙測寄送失敗。

## 設定檔
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

## 補充
### 關於 CORS
`CORS` 是 `HTTP` 中，限制不同網域的來源存取資源的機制，透過程式碼發出的請求都會被 `CORS` 阻擋下來，需要透過設定 `Access-Control-Allow-Origin` 來指定能接受的網域。

該協定的詳細可以參考 [這篇文章][CORS]

### 關於 http headers
一個 http 請求會攜帶一些 headers 供 host 端辨識，除了基本的 safelist headers 以外，未透過 `Access-Control-Allow-Headers` 設定的 headers 都會被 CORS 阻擋下來。

safelist 可以參考 [這篇文章][CORS-safelist]

### 關於 preflight
在特定情況下，client 會在發出實際請求前先發出預檢 (`preflight`)，詢問 host 是否接受該請求，多數情況會由瀏覽器或程式自動執行，不用自行設定。

## 參考
- [otlp receiver]
- [CORS]
- [CORS-safelist]


[otlp receiver]: https://github.com/open-telemetry/opentelemetry-collector/tree/main/receiver/otlpreceiver
[CORS]: https://developer.mozilla.org/zh-TW/docs/Web/HTTP/CORS
[CORS-safelist]: https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header