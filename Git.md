Git相关

配置

```java
ssh-keygen -t rsa -C "git@example.com"
// 打开 ~/.ssh/id_rsa.pub，复制生成的key配置至github
```

解决push时需要输入用户名和密码的问题

```
git remote add origin https://github.com/zhenghanqing/note.git
替换为
git remote add origin git@github.com:zhenghanqing/note.git
```



