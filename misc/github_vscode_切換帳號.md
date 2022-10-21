## MacOS 上 V.S. Code 切換 github 帳號方法

###事前準備

#### 1. 產生 ssh key

產生 ssh key A

```
ssh-keygen -t rsa -C "yves.chen@axv.bz"
```

設定存放路徑與名稱

```
Enter file in which to save the key (/Users/yves/.ssh/id_rsa):
>> .ssh/id_rsa_github_axv
```

產生 ssh key B

```
ssh-keygen -t rsa -C "personal@email.adress"
```

設定存放路徑與名稱

```
Enter file in which to save the key (/Users/yves/.ssh/id_rsa):
>> .ssh/id_rsa_github_personal
```

#### 2. 將公鑰新增至 github 上

查看公鑰

```
cat .ssh/id_rsa_github_axv.pub
```

新增至 [github](https://github.com/settings/ssh/new)


#### 3. 設定 .ssh/config

開啟設定檔

```
vim .ssh/config
```

參考設定

```
## github
# personal
Host github_personal
  HostName github.com
  User myname
  IdentityFile ~/.ssh/id_rsa_github_personal

# axv
Host github_axv
  HostName github.com
  User yves
  IdentityFile ~/.ssh/id_rsa_github_axv
```

#### 4. 測試是否成功

於 terminal 測試是否成功

```
ssh -T git@github_axv
>> Hi yveschen27182! You've successfully authenticated, but GitHub does not provide shell access.
```

### 專案部分

#### 1. initialize repo



#### 2. 取得 repo url

至 github repo 複製 ssh url


#### 3. 專案 git 設定

設定使用者與信箱

```
git config user.name "your name"
git config user.email "your email"
```


連接 repo

- git@<先前設定的 host>:\<repo 位置>

```
git remote add origin git@github_axv:yveschen27182/example.git
```

完成後即可綁定專案與 repo