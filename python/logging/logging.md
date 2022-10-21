## python logging 簡易操作說明
### 概要
logging 是 python 標準庫中提供的日誌套件
此篇文章介紹 logging 的基本使用方法

### 操作
#### 基本

1. import logging

```py
import logging
```

2. 產生日誌
    - 日誌預設有以下等級
        - debug
        - info
        - warning
        - error
        - critical

```py
# 使用 logging.<level> 即可產生日誌
logging.warning('Watch out!')
logging.info('hello!')
logging.error('error')
```

#### 日誌設定
1. 創建 logger
    - 根據[官方文件][1]說明，**永遠不要直接實體化 logging**
    - 因此需要先創建 logger 再對 logger 進行設定

```py
# 取得名為 myLogger 的 logger
# 若 myLogger 不存在，則會創建一個新的 myLogger
my_logger = logging.getLogger('myLogger')

# 使用 my_logger 產生日誌
my_logger.info('foo')
```

2. 設定 logger
    - setLavel, setFormatter 等可以對 logger 進行個別設定
    - basicConfig 可以一次對 logger 所有項目設定
    - 可設定項目請見[官方文件][2]

```py
my_logger.basicConfig(filename = 'example.log',
                    filemode = "w",
                    format = "[%(levelname)-5s %(asctime)s] : %(message)s",
                    datefmt = "%Y-%m-%d %H:%M:%S",
                    encoding = 'utf-8',
                    level = logging.INFO)
```

3. fileConfig
    - 可以使用外部設定檔進行 logger 設定
    - 根據[官方文件][3]說明，fileConfig() 為舊接口，因此建議改用 dictConfig()
> The fileConfig() API is older than the dictConfig() API and does not provide functionality to cover certain aspects of logging.
... 
so it’s worth considering transitioning to this newer API when it’s convenient to do so.
 
4. dictConfig
    - 請見下一章節

#### 設定文件

1. dictConfig
    - 使用 dictConfig 可以由 dict 讀取設定
    - 但由於設定較複雜，因此建議寫成 YAML (或其他格式) 後再讀入
2. Yaml 範本
    - 設定檔有四個基本要素 (必填)
        - version
        - formatters
        - handlers
        - loggers
    - format 參數可以參考[此處][4]
    - class 的類型可以參考[此處][5]
```yml
# version 目前固定為 1，未來可能釋出新版本
version: 1

# formatter 處理輸出格式
formatters:
  # 自訂名稱
  standard:
    # class 固定為 logging.Formatter
    class: logging.Formatter
    # format 為輸出格式
    format: '[%(levelname)-5s %(asctime)s] : %(message)s'
    # datefmt 為時間格式
    datefmt: '%Y-%m-%d %H:%M:%S'
  myFormatter:
    class: logging.Formatter
    format: <my format>
    datefmt: <my datefmt>

# handler 創建告警設定模組
handlers:
  # 自訂名稱
  console:
    # class 為輸出方式
    class: logging.StreamHandler
    # formatter 為上方定義的 formatter
    formatter: standard
    # 告警觸發層級
    level: INFO
    # 預設為 stderr
    stream: ext://sys.stdout
  file:
    class: logging.handlers.RotatingFileHandler
    formatter: standard
    level: INFO
    # 輸出檔案名稱
    filename: output.log
    # 輸出模式: [w]rite, [a]ppend
    mode: a
    # 編碼
    encoding: utf-8
    # rotating 設定
    maxBytes: 500000
    backupCount: 4
  myHandler:
    class: logging.<class>
    formatter: myFormatter
    level: DEBUG

# 創建 logger
loggers:
  # 自訂名稱
  main:
    # 最低觸發層級，與 handler 取層級較高者作為門檻
    level: INFO
    # 該 logger 採用之 handler
    handlers:
      - console
      - file
  myLogger:
    level: DEBUG
    handlers:
      - myHandler

```

3. 讀取設定
    - 安裝 PyYaml 套件
    - 讀取 .yml
```sh
# shell
pip install PyYaml
```

```py
import yaml
import logging.config

# 開啟 yaml
with open (<your yaml>, 'r') as file:
    # 讀取 yaml
    config = yaml.safe_load(file)
    # 進行 logging 設定
    logging.config.dictConfig(config)
```

4. 選取 logger
    - 僅需於 `main.py` 進行設定
    - 其他 module 直接 getLogger 即可
```py
# 取得名為 main 的 logger
logger = logging.getLogger('main')

# 取得名為 myLogger 的 logger
my_logger = logging.getLogger('myLogger')

# 取得名為 __main__ 的 logger
# if __name__ == '__main__'
main_logger = logging.getLogger(__name__)
```



### 參考
- 官方說明文件
    - [官方文件][n]

[1]: https://docs.python.org/3/library/logging.html#logger-objects
[2]: https://docs.python.org/3/library/logging.html#logging.basicConfig
[3]: https://docs.python.org/3.10/library/logging.config.html#configuration-file-format
[4]: https://docs.python.org/3/library/logging.html#logrecord-attributes
[5]: https://docs.python.org/3/library/logging.handlers.html#logging.FileHandler
[n]: https://docs.python.org/3/library/logging.html