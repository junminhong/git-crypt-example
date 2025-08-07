# git-crypt
git-crypt 可以讓你設定哪些檔些上傳到 github 時會自動做加密的一個工具

這個 repository 主要實作透過三把 key file 加密不同 service 的 config

## 安裝套件
```bash
brew install git-crypt
```

## 使用方式
### 製作一把鑰匙
```bash
# 可以直接初始化，那就會直接拿到一把 default key
git-crypt init
# 或者可以透過指定的方式去生成 key file
git-crypt init -k key_filename
```

### 設定要被加密的檔案
```bash
# 建立一個 .gitattributes 的檔案
vim .gitattributes

# 填入以下資訊
# 自己本身不需要做加密
.gitattributes !filter !diff

# 定義每個檔案應該使用哪一把 key 做加密
a_service/main.txt    filter=git-crypt-A   diff=git-crypt-A
b_service/main.txt    filter=git-crypt-B   diff=git-crypt-B
c_service/main.txt    filter=git-crypt-C   diff=git-crypt-C
```

### 上傳檔案
```bash
# 都建立完相關檔案後
# 將檔案 commit 後 push 到 github repository
git add .
git commit -m "feat: init"
git push origin main
```
接著請到 github respository 查看你會發現檔案都已經被加密過了
例如：[這個檔案](https://github.com/junminhong/git-crypt-example/blob/main/a_service/main.txt)


### 解密檔案
```bash
# 為了測試我們重新 clone github respository
cd /tmp
git clone git@github.com:junminhong/git-crypt-example.git
cd git-crypt-example
```
查看這個檔案 `git-crypt-example/a_service/main.txt`，這時候你會發現看不到檔案內容，你需要用剛剛加密的 key 來做解密
```bash
# 把剛剛在專案內的 key 複製到新的專案裡面
cp -r git-crypt-example/.git/git-crypt/keys /tmp/git-crypt-example/.git/git-crypt

# 接著我們就可以拿這個 key 來解密檔案了
# 對應的 key 會根據 .gitattributes 去解對應的檔案
git-crypt unlock .git/git-crypt/keys/a
git-crypt unlock .git/git-crypt/keys/b
git-crypt unlock .git/git-crypt/keys/c
```

### 加密檔案
```bash
# 全部加密，這會根據剛剛的 .gitattributes 檔案決定
git-crypt lock -a
```

## 結論
個人覺得這套工具挺好用的，可以讓一些敏感性的設定檔跟著進 git，同時也擁有版本控制的優點，也可以減少一些 KMS 的成本支出，也是一種不錯的選擇
