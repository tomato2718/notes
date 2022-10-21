###.wsdl 語法紀錄

```
<definations>	# wsdl 的設定資訊，類似 xml 的 descriptoin
targetNamespace	# 宣告此檔案參照的命名空間
xmlns			# 宣告沒有前綴的情況下預設命名空間
xmlns:var		# 宣告參數，var 可以是任意變數名稱
name: 			# ??
```
```
<type>			# 宣告這份檔案的資料型態
<import>		# 導入內容
schemaLocation	# 導入 schema 的來源位置
namespace		# 導入 schema 的命名空間 (與導入檔案之 targetNamespace 相同)
```
```
<message>
```
```
<portType>
```
```
<binding>
```
```
<port>
```
```
<service>
```