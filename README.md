# web_notes

### ssh 跳轉 Bastion Host
參考 [SSH跳轉](https://ouyang.me/posts/using-ansible-with-a-bastion-host/)

```admin --> Bastion Host --> internal1 、 internal2```



```
    Host internal1.example.com internal2.example.com
        User user
        Port 23456
        IdentityFile=keyfile_internal
        ProxyCommand ssh -qaY -i keyfile_bastion -p 12345 user@bastion.example.com 'nc -w 14400 %h %p'
```

- 後續即可
```ssh user@internal1```


### ssh-agent
- ssh-agent 有一個 forwarding 功能，它可以在不透過網路傳送金鑰的情況下，讓遠端的伺服器使用本地端的金鑰進行認證，金鑰本身只需要儲存一份在使用者端的電腦中
- 使用 SSH Agent 很方便也很直觀，但它其實是存在安全隱憂的，起因於 agent service 於連線時會在 /tmp/ 目錄建立一個檔案 /tmp/ssh-xxxxxx/agent.xxxx，在正常的情況下，它會用原使用者透過 ssh-add 所設定的密鑰對，替我們回答目的伺服器所發起的密鑰驗證要求。也因如此，惡意的使用者如果取得讀取這個檔案的權限，並將這個檔案的路徑設定在自己的環境變數 SSH_AUTH_SOCK 裡，就可以在沒有得到實際密碼內容的情況下，借用這個機制來替他回答任何驗證要求，此時受害者所有在 ssh-add 裡加入的服務密鑰，將在這段期間大門敞開。

#### Better Solution
- 所幸的是，我們可以利用較安全的 ProxyCommand 來連線，其不會留下一個檔案在使用者的 /tmp/ 目錄下，接續 (2)，這次改用以下指令建立連線。
```ssh -o 'ProxyCommand ssh ec2-user@bastion_path nc %h %p' ec2-user@ServerA_path```
這個方法可以讓我們直接登入遠端 web server，省略第二次的 ssh 指令
