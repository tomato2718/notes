## Suds 簡易操作說明
### 概要
Suds 是提供 python 建立 web service 客戶端的第三方套件，此篇文章介紹 Suds 的基本使用方法

### 操作

#### 基本設定
1. 安裝 suds
```sh
pip install suds
```

2. 基本功能僅需 import Client

```py
from suds.client import Client
```

3. 導入 wsdl 文件

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
4. 設定 host 位址 

```py
client.set_options(location = <host url>)
```


#### WS-Security 設定
1. 待測試

```py
from suds import wsse
security = wsse.Security()
token = wsse.UsernameToken(self.__user['user']self.__user['passwd'])
token.setnonce()
token.setcreated()
token.setnonceencoding(True)
token.setpassworddigest('digest')
security.tokens.append(token)
client.set_options(wsse=security)
```

#### 封裝訊息

1. 取得 wsdl 文件

```py
# 也可以使用其他輸出方法
print(client.__str__())
```

2. 解讀，參考以下範例
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

3. 簡單資料型態 - 直接填入
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

4. 複雜資料型態 - 建立 facotry
    - 對於 complex 類型的參數，會需要建立 factory 來儲存
    - 此處以 T 企業 esb-ws-client 案為例

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

5. SOAP header
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
1. 發送服務請求
    - **注意：** 須先將所有設定設置完畢後再發送請求

```py
# 以 getPercentBodyFat() 為例
# 使用 client.service.<method> 來發送請求
# result 接收 host 回傳結果
# 填入方法參考以下

# 直接依序填入
result = client.service.getPercentBodyFat('jeff', 68, 170)
# 使用 keyword
result = client.service.getPercentBodyFat(name='jeff', height=68, weight=170)
# 建立 dict 後使用 ** 拆解
d = dict(name='jeff', height=68, weight=170)
result = client.service.getPercentBodyFat(**d)
```

#### 其他
1. 更多功能請參考[官方文件][1]

### 參考
- 官方說明文件: 
    - [官方文件][1]
- 其他可替代套件: 
    - [Zeep][2]
    - [PySimpleSOAP][3]

[1]: https://suds.readthedocs.io/en/latest/index.html
[2]: https://docs.python-zeep.org/en/master/
[3]: https://github.com/pysimplesoap/pysimplesoap