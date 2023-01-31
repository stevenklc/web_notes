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
