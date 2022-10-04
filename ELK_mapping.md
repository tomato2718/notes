1. 數值資料會很講求要精確使用 short, long, byte 之類比較少見的數值型態嗎，或是都用 integer 和 float 就好？

2. 一個欄位可能有 0 ~ n 筆資料的情況下 (類似list)，該使用哪個資料型態？在官方說明文件看到 arrays 類是可以不用特別設定？
3. elasticsearch 有(或需要進行) not null 的設定嗎?
4. 我自己做了分類和實例，照這樣有哪些是需要更正的地方嗎？

```
用 measurement scale 下去分
Nominal: keyword, text, boolean
Ordinal: ?
Interval: integer, float
Ratio: integer, float
```

用 titanic 公開資料集 試著建立了一份資料

```
PassengerId (ID): keyword
Survived (是否生存): boolean
Pclass (票等): keyword
Name (姓名): text
Sex (性別): boolean
Age (年齡): integer
SibSp (同輩家屬數量): integer
Parch (不同輩家屬數量): integer
Ticket (票號): text
Fare (票價): float
Cabin (艙房號碼): ?
Embarked (登船地): keyword
```