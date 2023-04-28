# OpenTelemetry SDK: Python 自動化部署方法
## 概要
OpenTelemetry 提供了自動化部署與手動部署兩種方法在 python 中導入 `otel sdk`，自動化部署可以透過使用 `otel` 提供的套件起動 `python` 專案，讓維護者不用修改程式碼即可採集遙測資料。

## 操作
### 環境準備
- 安裝 `otel` 提供的 `python API` 與 `SDK`
```sh
>>> pip install opentelemetry-distro opentelemetry-exporter-otlp
```
- 安裝必要套件
    - 此動作會偵測該系統上已經安裝的套件，並自動抓取 `otel` 對應套件
```sh
>>> opentelemetry-bootstrap -a install
```
- 啟動目標 app
    - 與普通啟動方式相同，僅需在前方加上 `opentelemetry-instrument`
```sh
# 加上 opentelemetry-instrument 即可
>>> opentelemetry-instrument python <python args> <your app> <app args>
# 實際使用會如以下
>>> opentelemetry-instrument python -OOm myapi --host 0.0.0.0 --port 8080
```

### 設定
`otel` 提供了兩種設定方法，選擇其中一種即可。

使用 `-h` 指令可以取得參數說明。
```sh
>>> opentelemetry-instrument -h
```
或是參考 [SDK 設定][SDK]。

#### 透過參數設定
- 直接在 `opentelemetry-instrument` 之後加上設定。
```sh
>>> opentelemetry-instrument \
    --traces_exporter console,otlp \
    --metrics_exporter console \
    --service_name myapi \
    --exporter_otlp_endpoint localhost:4318 \
    python -OOm myapi --host 0.0.0.0 --port 8080
```


#### 透過環境變數設定
- 將參數設定為環境變數。
    - 將參數全部改為大寫，並加上 `OTEL_` 前綴。
```sh
# 此處環境變數設定方法僅供參考
>>> export OTEL_TRACES_EXPORTER=console,otlp
>>> export OTEL_SERVICE_NAME=myapi
>>> export OTEL_EXPORTER_ENDPOINT=localhost:4318
>>> pentelemetry-instrument python -OOm myapi --host 0.0.0.0 --port 8080
```

#### 重要設定項目
- `OTEL_SERVICE_NAME`：該服務的名稱。
- `OTEL_TRACES_EXPORTER`：指定 `traces` 的寄送方式。
    - `otlp`：使用 `otlp` 寄送。
    - `console`：輸出至裝置 (debug 用)。
- `OTEL_METRICS_EXPORTER`：指定 `metrics` 的寄送方式。
    - `otlp`：使用 `otlp` 寄送。
    - `console`：輸出至裝置 (debug 用)。
- `OTEL_LOGS_EXPORTER`：指定 `logs` 的寄送方式。
    - `otlp`：使用 `otlp` 寄送。
    - `console`：輸出至裝置 (debug 用)。
- `OTEL_EXPORTER_OTLP_ENDPOINT`：寄送所有目標的位址。
- `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT`：指定 `traces` 的寄送目標。
    - 注意：使用 `otlp/http` 時須在位址最後加上 `/v1/traces`。
- `OTEL_EXPORTER_OTLP_METRICS_ENDPOINT`：指定 `metrics` 的寄送目標。
    - 注意：使用 `otlp/http` 時須在位址最後加上 `/v1/metrics`。
- `OTEL_EXPORTER_OTLP_LOGS_ENDPOINT`：指定 `logs` 的寄送目標。
    - 注意：使用 `otlp/http` 時須在位址最後加上 `/v1/logs`。

## 補充
### 關於寄送目標
建議於本機建立 `otel agent` 或 `otel collector`，直接將資料寄送至 `collector`，再由 `collector` 進行處理或轉發。

### 關於遙測資料
自動化部署僅會採集基本監控項目，若需要進一步資料，請參考 [手動部署][manual] 方法。

## 參考
- [SDK 文件]
- [SDK 設定][SDK]
- [exporter 協定]


[SDK]: https://opentelemetry.io/docs/concepts/sdk-configuration/general-sdk-configuration/
[manual]: https://opentelemetry.io/docs/instrumentation/python/manual/
[SDK 文件]: https://opentelemetry.io/docs/instrumentation/python/automatic/
[exporter 協定]: https://opentelemetry.io/docs/reference/specification/protocol/exporter/