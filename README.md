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
