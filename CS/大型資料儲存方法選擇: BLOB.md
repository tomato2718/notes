# 大型資料儲存方法選擇: BLOB

## 情境

- 資料結構複雜
  - 以結構化保存開銷大
- 該資料不會用來搜尋/索引
  - 可以直接編碼為 bytes 以減少保存開銷和複雜度

## 資料範例

```json
{
    "description": "這是一段說明文字",
    "coordinate": [
        [31, 41],
        [15, 92],
        [65, 35],
        [89, 79],
        [32, 38],
        [46, 26]
    ]
}

```

## 解決方案

### 直接以 JSON 方式輸出

- 將 JSON 輸出為字串，並移除空格、換行符號

```plaintext
"{"description":"這是一段說明文字","coordinate":[[31,41],[15,92],[65,35],[89,79],[32,38],[46,26]]}"
```

- 以 utf8 編碼字串：佔用 105 bytes

```plaintext
7b226465736372697074696f6e223a22e98099e698afe4b880e6aeb5e8aaaae6988ee69687e5ad97222c22636f6f7264696e617465223a5b5b33312c34315d2c5b31352c39325d2c5b36352c33355d2c5b38392c37395d2c5b33322c33385d2c5b34362c32365d5d7d
```

優點：

- 實作簡單
- 資料直接可讀

缺點：

- 資料量較龐大
  - 包含了鍵
  - 將數值等資料都以字串型態保存

### 以 Protocol Buffer 序列化輸出

- 定義 Protocol Buffer

```proto
syntax = "proto3";

message Example {
    string description = 1;
    repeated Point coordinate = 2;
}

message Point {
    int32 x = 1;
    int32 y = 2;
}
```

- 以對應結構序列化資料

```plaintext
1 LEN 24
    這是一段說明文字
2 LEN 04 
    1 VARINT 31
    2 VARINT 41
2 LEN 04
    1 VARINT 15
    2 VARINT 92
2 LEN 04
    1 VARINT 65
    2 VARINT 35
2 LEN 04
    1 VARINT 89
    2 VARINT 79
2 LEN 04
    1 VARINT 32
    2 VARINT 38
2 LEN 04
    1 VARINT 46
    2 VARINT 26
```

- 將序列化資料編碼：佔用 62 bytes

```plaintext
0a18
    e98099e698afe4b880e6aeb5e8aaaae6988ee69687e5ad97
1204
    081f
    1029 
1204
    080f
    105c
1204
    0841
    1023
1204
    0859
    104f
1204
    0820
    1026
1204
    082e
    101a
```

優點：

- 轉換還原資料具結構性
- 轉換效率佳
  - 捨棄鍵：資料編號 (15之內) 和型別佔用 1 byte；16~2047 佔用 2 byte
  - 數值資料處理較佳：n byte 可以保存 2^(7*n) - 1 的數值

缺點：

- 資料無法直接閱讀
- 實作比較複雜
