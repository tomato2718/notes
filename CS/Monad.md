# Monad

> monads are just monoids in the category of endofunctors, what's the problem?

## 概念

Monad 是來自於函數式編程 (Functional Programming, FP) 的概念

主要有兩個特徵：

1. `map`：接收 `(T) => U` 的函式，將自身 `Monad[T]` 映射為 `Monad[U]` 的方法
2. `flat_map`：接收 `(T) => Monad[U]` 的函式，將自身 `Monad[T]` 映射為 `Monad[U]` 的方法

透過這兩個特徵，可以更容易對值進行運算

## 以 Option[T] 為例

```py
class Option[T]:
    _value: tuple[T, ...]

    def __init__(self, value: tuple[T, ...] = (), /) -> None:
        self._value = value

    @staticmethod
    def some(x: T, /) -> "Option[T]":
        return Option((x,))

    @staticmethod
    def none() -> "Option[T]":
        return Option()

    def map(self, fn: Callable[[T], U], /) -> "Option[U]":
        return Option(tuple(fn(x) for x in self._value))

    def flat_map(self, fn: Callable[[T], "Option[U]"]) -> "Option[U]":
        try:
            return next(fn(x) for x in self._value)
        except StopIteration:
            return Option()

    def is_some(self) -> bool:
        return len(self._value) != 0

    def is_none(self) -> bool:
        return not self.is_some()

    def unwrap(self) -> T:
        try:
            return next(iter(self._value))
        except StopIteration:
            raise ValueError("Unwrap on none")

    def unwrap_or(self, default: T, /) -> T:
        try:
            return self.unwrap()
        except ValueError:
            return default


a = Option[int].some(1)
a = (
    a
    .map(lambda x: x + 1)
    .map(lambda x: x * 2)
    .map(lambda x: x + 3)
)
assert a.unwrap() == 7

b = Option[int].none()
b = (
    b
    .map(lambda x: x + 1)
    .map(lambda x: x * 2)
    .map(lambda x: x + 3)
)
assert b.is_none()

c = Option[int].some(1)
c = (
    c
    .map(lambda x: x + 1)
    .flat_map(lambda _: Option[int].none())
    .map(lambda x: x + 3)
)
assert c.is_none()
```

## 以 Result[T] 為例

同理，可以構造出 `Result[T, E]`

```py
class Result[T, E]:
    _value: tuple[T, ...]
    _error: tuple[E, ...]

    def __init__(
        self, *, value: tuple[T, ...] = (), error: tuple[E, ...] = ()
    ) -> None:
        self._value = value
        self._error = error

    @staticmethod
    def ok(x: T, /) -> "Result[T, E]":
        return Result(value=(x,))

    @staticmethod
    def err(e: E, /) -> "Result[T, E]":
        return Result(error=(e,))

    def map(self, fn: Callable[[T], U], /) -> "Result[U, E]":
        return Result(value=tuple(fn(x) for x in self._value), error=self._error)

    def flat_map(self, fn: Callable[[T], "Result[U, E]"]) -> "Result[U, E]":
        try:
            return next(fn(x) for x in self._value)
        except StopIteration:
            return Result(error=self._error)

    def is_ok(self) -> bool:
        return len(self._value) != 0

    def is_err(self) -> bool:
        return not self.is_ok()

    def unwrap(self) -> T:
        try:
            return next(iter(self._value))
        except StopIteration:
            raise ValueError("Unwrap on err")

    def unwrap_or(self, default: T, /) -> T:
        try:
            return next(iter(self._value))
        except StopIteration:
            return default

    def unwrap_err(self) -> E:
        try:
            return next(iter(self._error))
        except StopIteration:
            raise ValueError("Unwrap error on ok")


type ErrorMessage = str


class User(TypedDict):
    user_name: str
    password: str
    balance: int


def get_user(user_name: str) -> Result[User, ErrorMessage]:
    USERS = {
        "admin": User(
            user_name="admin",
            password="admin",
            balance=100,
        )
    }
    try:
        return Result[User, ErrorMessage].ok(USERS[user_name])
    except KeyError:
        return Result[User, ErrorMessage].err("User do not exists.")


def auth(input_password: str, user: User) -> Result[User, ErrorMessage]:
    if input_password == user["password"]:
        return Result[User, ErrorMessage].ok(user)
    else:
        return Result[User, ErrorMessage].err("Permission denied")


def get_balance(username: str, password: str) -> int:
    balance = (
        get_user(username)
        .flat_map(lambda user: auth(password, user))
        .map(lambda user: user["balance"])
    )
    if balance.is_ok():
        return balance.unwrap()
    else:
        raise Exception(balance.unwrap_err())


assert get_balance("admin", "admin") == 100 # ok
try:
    get_balance("admin", "foo")
except Exception as e:
    print(e)  # Permission denied

try:
    get_balance("foo", "bar")
except Exception as e:
    print(e)  # User do not exists.
```
