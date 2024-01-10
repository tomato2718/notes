# 關於 `__exit__` 的行為

## 概要

這陣子在使用 `with` 語句時，發現 `__exit__` 會造成語句中宣告的參數有未繫結的可能性。

如以下範例：

```py
class Foo:
    bar = "bar"

    def __enter__(self) -> Self:
        return self

    def __exit__(self, *args) -> bool:
        do_something()
        return True

with Foo() as foo:
    example = foo.bar

print(example) # example may be unbound
```

在網路上也找不太到文件，因此做了一些測試

## 說明

### 如果是要離開前後文管理器

```py
class Foo:
    bar = "bar"

    def __enter__(self) -> Self:
        return self

    def __exit__(self, *args) -> None:
        do_something()

with Foo() as foo:
    example = foo.bar

print(example)
# bar
```

### 如果是要壓制例外

```py
class Ignore:
    def __enter__(self) -> None:
        pass

    def __exit__(self, *args) -> bool:
        return True

with Ignore():
    raise Exception()

print("foo")
# foo
```

### 解釋

經過測試後，發現 `__exit__` 方法會有以下兩種行為。

1. 在回傳任何屬於 `True` 的資料型態時，會壓制 `with` 語句中發生的例外，直接脫離區塊。
    - 如：`True`, `1`, `"foo"`, `["bar"]`
2. 在回傳任何屬於 `False` 的資料型態時，會照常提出例外。
    - 如：`None`, `False`, `0`, `""`, `[]`

若將回傳型態提示為 `bool`，同時會存在 `True` 的壓制與 `False` 的照常提出例外兩種可能性。

編輯器會進而判斷語句中的操作有未完成的可能性，進一步提出未繫結可能性的警告。
