# [otel] Python instrument 失敗 - Instrumenting of elasticsearch failed
## 環境資訊
### 系統
- `Python 3.10.10`
### Python 模組
- `elastic-transport 8.4.0`
- `elasticsearch 8.5.0`
- `opentelemetry-api 1.18.0`
- `opentelemetry-distro 0.39b0`
- `opentelemetry-instrumentation-elasticsearch 0.39b0`

## 問題描述
### 錯誤資訊
- 在程式中包含 elasticsearch client 時，使用 OpenTelemetry 自動部署。
- 使用 `opentelemetry-instrument` 執行 python 專案時，顯示 `Instrumenting of elasticsearch failed` 造成整個 instrument 失敗。

### 問題發生原因
- `opentelemetry-instrumentation-elasticsearch` 在調用 `elasticsearch` 模組時，傳入了錯誤的參數。
- `Transport` 於 `elasticsearch 8` 已經獨立為 `elastic-transport`，而 `opentelemetry-instrumentation-elasticsearch` 尚為更新語法。

> **注意:** 這個問題可能在未來版本被修正 

### 錯誤內容參考
```sh
Instrumenting of elasticsearch failed
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/auto_instrumentation/sitecustomize.py", line 84, in _load_instrumentors
    distro.load_instrumentor(entry_point, skip_dep_check=True)
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/distro.py", line 63, in load_instrumentor
    instrumentor().instrument(**kwargs)
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/instrumentor.py", line 108, in instrument
    result = self._instrument(  # pylint: disable=assignment-from-no-return
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/elasticsearch/__init__.py", line 140, in _instrument
    _wrap(
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 895, in wrap_function_wrapper
    return wrap_object(module, name, FunctionWrapper, (wrapper,))
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 841, in wrap_object
    (parent, attribute, original) = resolve_path(module, name)
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 829, in resolve_path
    original = lookup_attribute(parent, attribute)
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 827, in lookup_attribute
    return getattr(parent, attribute)
AttributeError: module 'elasticsearch' has no attribute 'Transport'
Failed to auto initialize opentelemetry
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/auto_instrumentation/sitecustomize.py", line 122, in initialize
    _load_instrumentors(distro)
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/auto_instrumentation/sitecustomize.py", line 88, in _load_instrumentors
    raise exc
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/auto_instrumentation/sitecustomize.py", line 84, in _load_instrumentors
    distro.load_instrumentor(entry_point, skip_dep_check=True)
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/distro.py", line 63, in load_instrumentor
    instrumentor().instrument(**kwargs)
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/instrumentor.py", line 108, in instrument
    result = self._instrument(  # pylint: disable=assignment-from-no-return
  File "/usr/local/lib/python3.10/site-packages/opentelemetry/instrumentation/elasticsearch/__init__.py", line 140, in _instrument
    _wrap(
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 895, in wrap_function_wrapper
    return wrap_object(module, name, FunctionWrapper, (wrapper,))
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 841, in wrap_object
    (parent, attribute, original) = resolve_path(module, name)
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 829, in resolve_path
    original = lookup_attribute(parent, attribute)
  File "/usr/local/lib/python3.10/site-packages/wrapt/wrappers.py", line 827, in lookup_attribute
    return getattr(parent, attribute)
AttributeError: module 'elasticsearch' has no attribute 'Transport'
```

## 解決方法
### 改為使用 elasticsearch 7.X 模組
#### 描述
- 修改模組版本

#### 操作
- 使用 `pip` 安裝 7.X 版本
```sh
>>> pip install "elasticsearch<8.0.0"
```

- 使用 `pip` 降級
    - 已經安裝該模組的情況
```sh
>>> pip install "elasticsearch<8.0.0" --upgrade
```

- 完成後即可正常啟動