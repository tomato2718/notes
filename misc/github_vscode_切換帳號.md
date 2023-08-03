# MacOS 上 VS Code 切換 github 帳號方法

## 事前準備
### 產生 ssh key
- 產生 ssh key A
```sh
>>> ssh-keygen -t rsa -C "tomato@example.com"
```

- 設定存放路徑與名稱
```sh
Enter file in which to save the key (/Users/yves/.ssh/id_rsa):
>> .ssh/id_rsa_github_tomato
```

- 產生 ssh key B
```sh
>>> ssh-keygen -t rsa -C "potato@example.com"
```

- 設定存放路徑與名稱
```sh
Enter file in which to save the key (/Users/tomato/.ssh/id_rsa):
>> .ssh/id_rsa_github_potato
```

### 將公鑰新增至 github 上

- 查看公鑰
```sh
>>> cat .ssh/id_rsa_github_tomato.pub
```

- 新增至 [github](https://github.com/settings/ssh/new)

### 設定 .ssh/config

開啟設定檔

- 開啟設定
```sh
>>> vim .ssh/config
```

- 參考設定
```plaintext
# github
## personal
Host github_potato
  HostName github.com
  User myname
  IdentityFile ~/.ssh/id_rsa_github_potato

## sub
Host github_tomato
  HostName github.com
  User yves
  IdentityFile ~/.ssh/id_rsa_github_tomato
```

### 測試是否成功
- 於 terminal 測試是否成功
  - 注意這邊使用者要使用 `git`
```sh
>>> ssh -T git@github_tomato
>> Hi tomato! You've successfully authenticated, but GitHub does not provide shell access.
```

## 專案部分

### initialize repo
```sh
>>> git init
```

### 取得 repo url
- 至 github repo 複製 ssh url


### 專案 git 設定
- 設定使用者與信箱

```sh
> git config user.name "your name"
> git config user.email "your email"
```

- 連接 repo
  - `git@<先前設定的 host>:\<repo 位置>`

```sh
>>> git remote add origin git@github_tomato:tomato/example.git
```
- 完成後即可綁定專案與 repo