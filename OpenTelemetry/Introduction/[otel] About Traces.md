# 關於 Traces
## 概要
這篇文章會深入介紹 Traces 的結構，以協助了解背後的運作邏輯與後端平台是如何解析 spans 的。

## 如何取得 Span
後端平台為了方便長期保存，通常會先將 spans 做預處理，在查看時也會是經過處理與解析的格式。

要直接取得 spans，可以使用 OTelCol 的 [fileexporter] 將 traces 輸出為檔案，再由檔案查看。

```yml
exporters:
  file/no_rotation:
    path: ./foo

  file/rotation_with_default_settings:
    path: ./foo
    rotation:

  file/rotation_with_custom_settings:
    path: ./foo
    rotation:
      max_megabytes: 10
      max_days: 3
      max_backups: 3
      localtime: true
    format: proto
    compression: zstd

  file/flush_every_5_seconds:
    path: ./foo
    flush_interval: 5
```

## Span 的結構
以下是兩個不同服務產生的 span，由 `checkout-service-stable` 對 `email-service-stable` 發起請求。

### checkout-service-stable
```json
{
  "resourceSpans": [
    {
      "resource": {
        "attributes": [
          {
            "key": "telemetry.sdk.language",
            "value": {
              "stringValue": "python"
            }
          },
          {
            "key": "telemetry.sdk.name",
            "value": {
              "stringValue": "opentelemetry"
            }
          },
          {
            "key": "telemetry.sdk.version",
            "value": {
              "stringValue": "1.18.0"
            }
          },
          {
            "key": "service.node.name",
            "value": {
              "stringValue": "a107"
            }
          },
          {
            "key": "service.name",
            "value": {
              "stringValue": "checkout-service-stable"
            }
          },
          {
            "key": "telemetry.auto.version",
            "value": {
              "stringValue": "0.39b0"
            }
          }
        ]
      },
      "scopeSpans": [
        {
          "scope": {
            "name": "opentelemetry.instrumentation.requests",
            "version": "0.39b0"
          },
          "spans": [
            {
              "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
              "spanId": "2d481948fbee4f30",
              "parentSpanId": "d013e9ff54bfcce4",
              "name": "HTTP POST",
              "kind": 3,
              "startTimeUnixNano": "1688022327772565412",
              "endTimeUnixNano": "1688022328157652874",
              "attributes": [
                {
                  "key": "http.method",
                  "value": {
                    "stringValue": "POST"
                  }
                },
                {
                  "key": "http.url",
                  "value": {
                    "stringValue": "http://email-service-stable.apm-demo.svc.cluster.local:80/email/"
                  }
                },
                {
                  "key": "http.status_code",
                  "value": {
                    "intValue": "202"
                  }
                }
              ],
              "status": {}
            }
          ]
        },
        {
          "scope": {
            "name": "opentelemetry.instrumentation.flask",
            "version": "0.39b0"
          },
          "spans": [
            {
              "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
              "spanId": "d013e9ff54bfcce4",
              "parentSpanId": "",
              "name": "/checkout/",
              "kind": 2,
              "startTimeUnixNano": "1688022325838289054",
              "endTimeUnixNano": "1688022328182880099",
              "attributes": [
                {
                  "key": "http.method",
                  "value": {
                    "stringValue": "POST"
                  }
                },
                {
                  "key": "http.server_name",
                  "value": {
                    "stringValue": "waitress.invalid"
                  }
                },
                {
                  "key": "http.scheme",
                  "value": {
                    "stringValue": "http"
                  }
                },
                {
                  "key": "net.host.port",
                  "value": {
                    "intValue": "80"
                  }
                },
                {
                  "key": "http.host",
                  "value": {
                    "stringValue": "10.10.1.106"
                  }
                },
                {
                  "key": "http.target",
                  "value": {
                    "stringValue": "/checkout/"
                  }
                },
                {
                  "key": "net.peer.ip",
                  "value": {
                    "stringValue": "10.10.1.107"
                  }
                },
                {
                  "key": "http.user_agent",
                  "value": {
                    "stringValue": "curl/8.1.0"
                  }
                },
                {
                  "key": "net.peer.port",
                  "value": {
                    "stringValue": "45946"
                  }
                },
                {
                  "key": "http.flavor",
                  "value": {
                    "stringValue": "1.1"
                  }
                },
                {
                  "key": "http.route",
                  "value": {
                    "stringValue": "/checkout/"
                  }
                },
                {
                  "key": "http.status_code",
                  "value": {
                    "intValue": "200"
                  }
                }
              ],
              "status": {}
            }
          ]
        }
      ]
    }
  ]
}
```

### email-service-stable

```yaml
{
  "resourceSpans": [
    {
      "resource": {
        "attributes": [
          {
            "key": "telemetry.sdk.language",
            "value": {
              "stringValue": "python"
            }
          },
          {
            "key": "telemetry.sdk.name",
            "value": {
              "stringValue": "opentelemetry"
            }
          },
          {
            "key": "telemetry.sdk.version",
            "value": {
              "stringValue": "1.18.0"
            }
          },
          {
            "key": "service.node.name",
            "value": {
              "stringValue": "a108"
            }
          },
          {
            "key": "service.name",
            "value": {
              "stringValue": "email-service-stable"
            }
          },
          {
            "key": "telemetry.auto.version",
            "value": {
              "stringValue": "0.39b0"
            }
          }
        ]
      },
      "scopeSpans": [
        {
          "scope": {
            "name": "opentelemetry.instrumentation.flask",
            "version": "0.39b0"
          },
          "spans": [
            {
              "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
              "spanId": "55244edc980b271d",
              "parentSpanId": "2d481948fbee4f30",
              "name": "/email/",
              "kind": 2,
              "startTimeUnixNano": "1688022322207474452",
              "endTimeUnixNano": "1688022322507137249",
              "attributes": [
                {
                  "key": "http.method",
                  "value": {
                    "stringValue": "POST"
                  }
                },
                {
                  "key": "http.server_name",
                  "value": {
                    "stringValue": "waitress.invalid"
                  }
                },
                {
                  "key": "http.scheme",
                  "value": {
                    "stringValue": "http"
                  }
                },
                {
                  "key": "net.host.port",
                  "value": {
                    "intValue": "80"
                  }
                },
                {
                  "key": "http.host",
                  "value": {
                    "stringValue": "email-service-stable.apm-demo.svc.cluster.local"
                  }
                },
                {
                  "key": "http.target",
                  "value": {
                    "stringValue": "/email/"
                  }
                },
                {
                  "key": "net.peer.ip",
                  "value": {
                    "stringValue": "192.168.82.64"
                  }
                },
                {
                  "key": "http.user_agent",
                  "value": {
                    "stringValue": "python-requests/2.31.0"
                  }
                },
                {
                  "key": "net.peer.port",
                  "value": {
                    "stringValue": "42790"
                  }
                },
                {
                  "key": "http.flavor",
                  "value": {
                    "stringValue": "1.1"
                  }
                },
                {
                  "key": "http.route",
                  "value": {
                    "stringValue": "/email/"
                  }
                },
                {
                  "key": "http.status_code",
                  "value": {
                    "intValue": "202"
                  }
                }
              ],
              "status": {}
            }
          ]
        }
      ]
    }
  ]
}
```

## 解說
可以從以上的範例中看到，span 分為兩個區塊，分別是 `resourceSpans` 和 `scopeSpans`。

### resourceSpans
resourceSpans 主要紀錄服務層級的資訊，像是服務所在節點的名稱、服務的名稱、SDK 的語言和類型等，這些參數一般稱為 resource attributes。

關於 ResourceSpans 的語義說明可以參考：[Resource 語義][resource]

### scopeSpans
scopeSpans 主要紀錄 span 層級的資訊，換個說法就是這個 span 真正要記錄的資料，scope 部分是產生 span 的方法、span 則是資料主體，接下來會以 span 為主要介紹內容。

#### span 的結構
```json
// checkout
{
  "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
  "spanId": "d013e9ff54bfcce4",
  "parentSpanId": "",
  "name": "/checkout/",
  "kind": 2,
  "startTimeUnixNano": "1688022325838289054",
  "endTimeUnixNano": "1688022328182880099",
  "attributes": []
}
// email
{
  "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
  "spanId": "55244edc980b271d",
  "parentSpanId": "2d481948fbee4f30",
  "name": "/email/",
  "kind": 2,
  "startTimeUnixNano": "1688022322207474452",
  "endTimeUnixNano": "1688022322507137249",
  "attributes": []
}
```

從上方的資料可以看到 span 有幾個基本的欄位：

- **tracesID:** 只要是同一次呼叫的 API，都會有相同的 traceID，後端平台透過此欄位將同一次呼叫的 span 集合在一起。
- **spanID:** 每個 span 都會有的 ID。
- **parentSpanID:** 前一層級的 span ID，可以看到 email 的 parentSpanID 欄位即是 chekout 的 spanID。
- **name:** 這個 span 的名稱，通常會是該服務的路徑。
- **kind:** span 的類型，會在下方獨立區塊介紹。
- **startTimeUnixNano:** 該服務被呼叫的時間。
- **endTimeUnixNano:** 該服務完成處理的時間，與上者結合就可以得出這次呼叫花費的時間。
- **attributes:** 夾帶的參數，會在下方獨立區塊介紹。

#### 關於 kind
kind 有很多種類型，其中最常見的就是 2 (SERVER) 和 3 (CLIENT)，如下方區塊：

```json
// Service A
{
  "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
  "spanId": "d013e9ff54bfcce4",
  "parentSpanId": "",
  "name": "/checkout/",
  "kind": 2
}
{
  "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
  "spanId": "2d481948fbee4f30",
  "parentSpanId": "d013e9ff54bfcce4",
  "name": "HTTP POST",
  "kind": 3
}
// Service B
{
  "traceId": "c80f31ec45ce21fc8d72bac53a534e42",
  "spanId": "55244edc980b271d",
  "parentSpanId": "2d481948fbee4f30",
  "name": "/email/",
  "kind": 2
}
```

checkout 先是收到了來自使用者的呼叫，此時是做為 Server 的角色被呼叫，因此 kind 為 2。

接著 checkout 在處理呼叫的過程中，又呼叫了 email 服務，此時會產生一個 span，而因為是做為 Client 端呼叫其他服務，所以此時的 kind 會是 3。

最後 email 收到了來自 checkout 的請求，進行處理時又產生了一個 kind 為 2 的 span。

運作方式會像下圖所示：

![spanKind]

#### 關於 attributes
attributes 是每個 span 夾帶的參數，用來提供後端平台與管理者分析使用。不同類型的 span 的參數會有所不同，關於每個參數的定義可以參考 [OTel 語義學][semantic]。


## 參考
- [fileexporter]
- [Resource Attribute][resource]
- [OTel semantic][semantic]

[fileexporter]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/fileexporter
[resource]: https://github.com/open-telemetry/semantic-conventions/tree/main/docs/resource
[spanKind]: ./img/spanKind.png
[semantic]: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/README.md