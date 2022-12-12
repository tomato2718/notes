## Suds 簡易操作說明
### 概要
Suds 是提供 python 建立 web service 客戶端的第三方套件，此篇文章介紹 Suds 的基本使用方法

### 操作

#### 基本設定
- 安裝 suds
```sh
pip install suds
```

- 基本功能僅需 import Client

```py
from suds.client import Client
```

- 導入 wsdl 文件

```py
url = 'http://<your wsdl>'
client = Client(url)
```

若 wsdl 為本地檔案，則需要轉換路徑

```py
from urllib.parse import urljoin
# file://<path>/*.wsdl
url = urljoin('file:', <path to wsdl>)
client = Client(url)
```
- 設定 host 位址 

```py
client.set_options(location = <host url>)
```


#### WS-Security 設定
- Suds 有提供部分 wsse 設定可供使用
    - 需要先 import wsse 模組

```py
from suds import wsse
```

- 建立 Security 物件

```py
security = wsse.Security()
```

- 使用者帳密設定
    - Suds 有提供以下 user token 設定

```py
# 使用 UsernameToken 建立標籤
token = wsse.UsernameToken('username', 'password')

# 設定 nonce
token.setnonce()
token.setnonceencoding(True)
# 設定 created 
token.setcreated()
# 設定 type 為 PasswordDigest
# 預設為 PasswordText
token.setpassworddigest('digest')
# 新增 token
security.tokens.append(token)
```

- 設定 Timestamp
    - [API][timestamp]

```py
# 建立 timestamp 標籤
# 預設值為現在時間，過期時間為 現在時間 + validity (sec)
timestamp = wsse.Timestamp(validity=300)
# 新增 token
security.tokens.append(timestamp)
```

- wsse 建立完後，新增至 client

```py
# 使用 set_options 設定
client.set_options(wsse=security)
```

#### 封裝訊息

- 取得 wsdl 文件

```py
# 也可以使用其他輸出方法
print(client.__str__())
```

- 解讀，參考以下範例
    - Methods 為該 ws 提供之接口
    - Types 為 ws 提供的複雜類型

```
Suds - version: 0.3.3 build: (beta) R397-20081121

Service (WebServiceTestBeanService) tns="http://test.server.enterprise.rhq.org/"
   Prefixes (1):
     ns0 = "http://test.server.enterprise.rhq.org/"
   Ports (1):
     (Soap)
       Methods:
         addPerson(Person person, )
         echo(xs:string arg0, )
         getList(xs:string str, xs:int length, )
         getPercentBodyFat(xs:string name, xs:int height, xs:int weight)
         getPersonByName(Name name, )
         hello()
         testExceptions()
         testListArg(xs:string[] list, )
         testVoid()
         updatePerson(AnotherPerson person, name name, )
   Types (23):
     Person
     Name
     Phone
     AnotherPerson
```

- 簡單資料型態 - 直接填入
    - 參考文件中 Methods 定義的方法所需參數
    - 服務請求類似 function，參數可以有不同填入方式
    - 簡單資料型態的參數可以直接填入

```py
# 以 getPercentBodyFat() 為例
# 此處僅介紹概念，發送請見後續章節

# 直接依序填入
getPercentBodyFat('jeff', 68, 170)
# 使用 keyword
getPercentBodyFat(name='jeff', height=68, weight=170)
# 建立 dict 後使用 ** 拆解
d = dict(name='jeff', height=68, weight=170)
getPercentBodyFat(**d)
```

- 複雜資料型態 - 建立 facotry
    - 對於 complex 類型的參數，會需要建立 factory 來儲存
    - 此處以 T 公司 esb-ws-client 案為例

```py
# 建立 factory 物件
# 使用 client.factory.create() 創建
# 參數為 Types 之內容，若有 prefix 需要一起填入
body = client.factory.create('ns14:NewAlarm')

# __str__() 同樣可以取得該物件訊息
print(body.__str__())
# __Keylist__ 僅會印出參數列表
print(body.__keylist__)

# 可以視 factory 為一物件，直接填入參數
body.alarmId = '12345'

# 若 facotry 中參數類型也是 complex
# 可以再創建一個 factory 作為參數，如以下範例
# alarmAdditionInfo = 
#      (AlarmAdditionInfo){
#         alarmNo = None
#         alarmName = None
#         abAlarm = None
#         massAlarm = None}

# 建立參數指定的 factory: (AlarmAdditionInfo)
alarm_info = client.factory.create('ns14:AlarmAdditionInfo')
# 一樣填入參數
alarm_info.alarmNo = '54321'
alarm_info.name = 'foo'

# 最後將子 factory 填入父 factory 即可
body.alarmAdditionInfo = alarm_info

```

- SOAP header
    - soap header 的填入方法也是使用 factory
    - 依照 4. 的方法建立後使用 `set_options` 填入即可

```py
# 創建 factory
header = client.factory.create('ns0:commonHeader')
header.messageFrom = 'foo'
# 填入 soapheader
client.set_options(soapheaders=header)
```

#### 發送服務請求
- 發送服務請求
    - 須先將所有設定設置完畢後再發送請求
    - **注意：** Suds 套件 (1.1.2) 目前存在 bug，服務最外層的參數要用 dict 填寫

```py
# 以 getPercentBodyFat() 為例
# 使用 client.service.<method> 來發送請求
# result 接收 host 回傳結果
# 最外層使用 complex 型態的話會產生無意義重複欄位

# 建立 dict 後使用 ** 拆解
d = dict(name='jeff', height=68, weight=170)
result = client.service.getPercentBodyFat(**d)
```

#### 其他
- Plugin
    - 若發送的訊息與 host 要求不符時，可以透過 plugin 過濾訊息
    - Suds 預設會將所有未填寫欄位都填入空值後傳送，須手動過濾
    - plugins 於建立 Client 時一併套用
    - 參考 [API][API]

```py
# 以 T 公司 esb-ws-client 案為例
# 案件中客戶端提供的 wsdl 定義了多餘欄位
# 但 host 不接受那些欄位，因此要透過 plugin 過濾

# 需要先 import plugin 模組
from suds.plugin import MessagePlugin

# 於建立 client 時新增 plugins 參數
# 資料情態為 list<class()> 可以填入多個 plugin
client = Client(url, plugins=[__RemoveExtra()])

# 建立要填入的 plugin
class __RemoveExtra(MessagePlugin):
    # marshalled 為 MessagePlugin 定義的方法
    # 能在訊息寄出前先修改 envelope
    # 更多方法請參考 API
    def marshalled(self, context):
        # 建立需移除的多餘欄位清單
        to_remove = ['sourceTime', 'objectType', 'objectId']
        for name in to_remove:
            # 參考下方 code block
            context.envelope.getChild('Body')[0].getChild(name).detach()
```

```xml
<!--detail>
    原始 envelope 參考
    此範例移除了部分欄位，僅保留重點部分
    上方 plugin 移除的目標為
    context.envelope  => <SOAP-ENV:Envelope>
    .getChild('Body') => <ns3:Body>
    ('Body')[0]       => <ns2:newAlarmNotificationRequest>
    .getChild(name)   => <ns0:sourceTime/> ...
    .detach()         => 移除
</detail-->
<SOAP-ENV:Envelope>
   <SOAP-ENV:Header>
      <com:commonHeader>
         <com:messageFrom>XXX</com:messageFrom>
         <com:messageTo>XXX</com:messageTo>
         <com:transactionId>XXX</com:transactionId>
         <com:messageTimeStamp>2022-11-14T10:30:37.055+08:00</com:messageTimeStamp>
      </com:commonHeader>
   </SOAP-ENV:Header>
   <ns3:Body>
      <ns2:newAlarmNotificationRequest>
         <ns0:sourceTime/>
         <ns0:objectType/>
         <ns0:objectId/>
         <ns2:alarmId>XXX</ns2:alarmId>
         <ns2:alarmType>XXX</ns2:alarmType>
         <ns2:perceivedSeverity>XXX</ns2:perceivedSeverity>
         <ns2:probableCause></ns2:probableCause>
         <ns2:alarmAdditionInfo>
            <ns2:alarmNo>123</ns2:alarmNo>
            <ns2:alarmName>XXX</ns2:alarmName>
            <ns2:abAlarm>XXX</ns2:abAlarm>
            <ns2:massAlarm>XXX</ns2:massAlarm>
         </ns2:alarmAdditionInfo>
      </ns2:newAlarmNotificationRequest>
   </ns3:Body>
</SOAP-ENV:Envelope>
```

- Raw response
    - 若 host 回傳的訊息會造成套件解析出錯或 plugin 難以處理時，可以改為接收 raw response
    - 直接於 set_options 設定
```py
# 接收 raw response
client.set_options(retxml=True)

# 此時接收到的結果會是 bytes
res = SomeService(**my_dict)
# 先對其 decode 轉換為字串
res.decode('utf-8')

# 之後可以使用 xml 套件解析與處理
import xml.etree.ElementTree as ET
tree = ET.fromstring(res)
```

- 更多功能請參考[官方文件][1]

### 參考
- 官方說明文件: 
    - [官方文件][1]
- 其他可替代套件: 
    - [Zeep][2]
    - [PySimpleSOAP][3]

[1]: https://suds.readthedocs.io/en/latest/index.html
[2]: https://docs.python-zeep.org/en/master/
[3]: https://github.com/pysimplesoap/pysimplesoap
[API]: https://suds.readthedocs.io/en/latest/suds.html#module-suds.plugin
[timestamp]: https://suds.readthedocs.io/en/latest/suds.html?highlight=timestamp#suds.wsse.Timestamp