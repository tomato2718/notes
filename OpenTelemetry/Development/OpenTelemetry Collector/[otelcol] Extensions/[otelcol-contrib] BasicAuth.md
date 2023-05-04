# [otelcol-contrib] 使用 BasicAuth 設定 Basic 認證
## 概要
多數情況，在將遙測資料寄送至後端平台時，會需要有使用者認證的 header，而 BasicAuth 擴充套件提供了快速設定 server 與 client 認證的功能。

**注意**：此套件目前不支援 http 連線。

**另一個注意**：server 與 client 不能同時定義於同個區塊，須建立為兩不同區塊。

## 部署方法
### 為 receiver 設定帳號密碼
- 在 receiver 處加入擴充套件
```yml
extensions:
  # 於擴充套件區塊建立 basicauth 擴充
  basicauth/server:
    # 為 receiver 設定 htpassed
    htpasswd: 
      # 使用檔案設定
      file: .htpasswd
      # 直接由文字設定
      inline: |
        ${env:BASIC_AUTH_USERNAME}:${env:BASIC_AUTH_PASSWORD}

receivers:
  otlp:
    protocols:
      http:
        # 宣告在此 receiver 使用建立好的擴充套件
        auth:
          authenticator: basicauth/server
processors:

exporters:

service:
  # 記得在 service 區塊加入建立好的擴充套件
  extensions: [basicauth/server]
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [otlp]
```
**注意**：若後端平台也使用 basicauth，可能使 request 發生衝突，此時不建議於此設定。

### 為 exporter 設定帳號密碼
- 於 exporter 處加入擴充套件
```yml
extensions:
  # 於擴充套件區塊建立 basicauth 擴充
  basicauth/client:
    # 設定為 client 端認證
    client_auth: 
      # 填入要使用的帳號密碼即可
      username: username
      password: password

receivers:
  otlp:

processors:

exporters:
  otlp:
    auth:
      # 宣告在此 receiver 使用建立好的擴充套件
      authenticator: basicauth/client

service:
  # 記得在 service 區塊加入建立好的擴充套件
  extensions: [basicauth/client]
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [otlp]
```

## 補充
### 若要在 http 環境使用 basic 認證
可以手動加入編碼過的 header
- 手動編碼
```sh
>>> echo -n <user>:<password> | base64 -w 0
ZGV2LXRlbmFudDpaR1YyTFhKbFlXUXR0000000000GNHOXNhV041TFhSbGMzUXRkRzl0000000000TZmRE5jUlMxNU16RmZOM0ZjTUNFNWV6UTROU3RzSlRjdA==
```
- 自行加入 header
```yml
exporters:
    otlp:
    endpoint:
    tls:
        insecure: true
    headers:
        # 剛剛編碼好的帳密
        # 使用 Basic 認證
        Authorization: "Basic ZGV2LXRlbmFudDpaR1YyTFhKbFlXUXR0000000000GNHOXNhV041TFhSbGMzUXRkRzl0000000000TZmRE5jUlMxNU16RmZOM0ZjTUNFNWV6UTROU3RzSlRjdA=="
```

### 無法找到 BasicAuth 擴充套件
請確認你是否使用社群版本 `otelcol-contrib`，官方版本並無提供此擴充套件。

## 參考
- [github repo]

[github repo]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/extension/basicauthextension
