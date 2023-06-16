# 關於 Observability

## 為什麼需要 Observability?

過去的服務大多為單一架構，透過語言內建的功能很容易就能追蹤執行流程與效能。但近年來隨著微服務的發展，一套系統被拆分為多個小型服務，讓 Monitoring (監控) 變得相對困難，因此有了 Observability (可觀察性) 的誕生。

Observability (可觀察性) 跟 Monitoring (監控) 的差別在於：
- **監控:** 事先對可能發生的情況建立機制，並在觸發門檻時發出告警給管理者。
- **可觀察性:** 透過在各服務部署對應工具取得特定指標，讓管理者透過檢視指標來確定服務是否正常運作。並在異常時透過指標深入追蹤問題發生原因。

## 什麼是 Observability?

![observability]

Observability 透過在服務間加入特定的工具來取得遙測 (Telemetry)，並寄送至後端的分析平台。讓管理者能在服務 **外部** 直接觀測服務 **內部** 的狀況。

Observability 由三個核心要素組成：
- **Metrics:** 程式或系統的 CPU, load, memory 等系統指標。
- **Traces:** 程式間溝通花費的時間, 對象等資訊。
- **logs:** 程式的運作日誌。

而 Observability 又有幾個主要面向：
- **Infrastructure:** 系統層級的資訊。
- **Application Performance Monitoring (APM):** 服務層級的資訊。
- **Real User Monitoring (RUM):** 使用者層級的資訊。

## Observability 的核心要素
### Metrics

Metrics 以數值的形式記錄系統的效能指標，讓後端的分析平台透過這些數值來繪製儀表板供管理者使用。

一個 metric 可能會長得像下方範本：

```json
{
    "service.type": [
      "system"
    ],
    "system.load.15": [
        0
    ],
    "data_stream.type": [
        "metrics"
    ],
    "metricset.name": [
        "load"
    ],
    "event.duration": [
        134538
    ],
    "system.load.norm.1": [
      0
    ],
    "system.load.norm.5": [
      0
    ],
    "system.load.norm.15": [
      0
    ],
    "system.load.1": [
      0
    ],
    "system.load.5": [
      0
    ],
}
```
### Traces

在程式對其他服務發起 **HTTP 請求** 時，會產生一個 span 用來記錄該請求的方法、目標、結果與花費時間等資訊，而將多個 span 串連在一起，就能形成一個 Trace 。

如下方的範本：

```json
{
    "name": "/checkout/",
    "context": {
        "trace_id": "0x10b8047bf66f397c799501e616573811",
        "span_id": "0xbb095b7d1f68042e",
        "trace_state": "[]"
    },
    "kind": "SpanKind.SERVER",
    "parent_id": "0x5f769e803cc64efe",
    "start_time": "2023-06-13T08:00:54.327224Z",
    "end_time": "2023-06-13T08:00:57.246596Z",
    "status": {
        "status_code": "UNSET"
    },
    "attributes": {
        "http.method": "POST",
        "http.server_name": "waitress.invalid",
        "http.scheme": "http",
        "net.host.port": 80,
        "http.host": "10.10.1.106",
        "http.target": "/checkout/",
        "net.peer.ip": "10.10.1.108",
        "http.user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36",
        "net.peer.port": "39658",
        "http.flavor": "1.1",
        "http.route": "/checkout/",
        "http.status_code": 200
    },
    "events": [],
    "links": [],
    "resource": {
        "attributes": {
            "telemetry.sdk.language": "python",
            "telemetry.sdk.name": "opentelemetry",
            "telemetry.sdk.version": "1.18.0",
            "service.node.name": "a108",
            "service.name": "checkout-service-alpha",
            "telemetry.auto.version": "0.39b0"
        },
        "schema_url": ""
    }
}
```

當中幾個比較重要的欄位：
- **context:** 包含了 trace_id 與 span_id。
    - **trace_id:** 同個動作中所有的 span 都會有相同的 trace_id，後端平台透過此 id 將這次動作產生的 span 串連在一起。
    - **span_id:** 每個 span 的 id，配合 parent_id 來辨識 span 在該 trace 中的先後順序。
- **parant_id:** 前一個服務的 span_id。
- **start_time/end_time:** 這個服務的開始與結束時間。
- **attributes:** span 層級的參數，通常是該服務的請求資訊。
- **resource:** 服務層級的參數，像是程式語言、節點名稱、服務名稱等。

### Logs

Logs 是各個服務在運作時產生的詳細資訊。在管理者發現服務有異常時，可以透過 traces 與 metrics 快速對應到目標的 log 來排除問題。

## Observability 的實作
### Infrastructure

![Infrastructure]

Infrastructure 管理系統層級的資訊，透過主機名稱等資訊與 APM 互相對應。通常是透過在節點上部署 agent 來採集資訊，並寄回資料庫保存。

採集項目：
- **metrics:** 主機 (節點) 的狀況，像是 CPU、記憶體等。
- **traces:** 無。
- **logs:** 無。

### Application Performance Monitoring (APM)

![APM]

APM 管理服務層級的資訊，透過在程式碼中 (或外層) 加入 agent 來採集該服務的遙測 (Instrumenting)，讓管理者分析服務並改善效能。

採集項目：
- **metrics:** 程式的狀況，像是回應時間、錯誤率等。
- **traces:** 延遲、回應狀況、路由等。
- **logs:** 程式日誌。

### Real User Monitoring (RUM)

![RUM]

RUM 管理使用者層級的資訊，透過在使用者請求的資源 (一般來說是網頁) 加入 JavaScript 編寫的 agent，取得使用者在使用該頁面時的資訊。

採集項目：
- **metrics:** 頁面的狀況，載入時間、渲染狀況等。
- **traces:** 使用者操作與互動的追蹤。
- **logs:** 無。

## 如何建立 Observability?

為服務建立 Observability，一般來說會需要四個動作。

- **Instrument:** 為服務或系統加入工具來採集遙測。
- **Pipeline:** 服務與後端平台之間的管線程式。
- **Database:** 儲存遙測的資料庫，通常與後端平台搭配選用。
- **Backend platforms:** 後端分析平台。

### Instrument

在目標服務或系統中加入採集工具的動作稱為 Instrument，目前多數是採用 OpenTelemetry (OTel) 進行，其他也有如：Jaeger, Zipkin 等套件能使用。

對不同類型的實作， Instrument 方法也有不同：

- **Infrastructure:** 在系統啟用 OTel Agent 或 MetricBeats 等套件。
- **APM:** 在程式中導入 OTel 函式庫或各個後端平台提供的函式庫。
- **RUM:** 在網頁或行動 app 中導入各個平台提供的函式庫。 (OTel 尚未有相關做法)

### Pipeline

Pipeline 是 Instrument 與後端平台之間的橋樑，雖非必要但多數情況還是建議使用。Pipieline 可以減輕服務的負擔並集中管理遙測 (接收、預處理、寄送等)。

OpenTelemetry Collector (OTelcol) 對多數後端平台都有直接支援，各個平台也有提供相關的套件。

### Database & Backend platforms

資料庫通常與後端平台配合，透過後端平台 (ElasticSearch, Grafana, Splunk, etc.) 可以將遙測視覺化並進行分析，讓管理者快速了解各個服務的執行狀態。

<!-- links -->
[observability]: ./img/observability.png
[infrastructure]: ./img/Infrastructure.png
[APM]: ./img/APM.png
[RUM]: ./img/RUM.png