# Git

# mac配置多账号

SSH key 可以让你在你的电脑和 Git 服务器之间建立安全的加密连接。一般你的ssh key存储在 这个目录下/Users/{username}/.ssh

## 1.cd /Users/{accountname}/.ssh

查看.ssh目录会有这样几个文件

```javascript
-rw-r--r--@ 1 {username}  staff   280B  8 30 15:12 config    
-rw-------  1 {username}  staff   3.2K  8 29 17:42 id_rsa
-rw-r--r--  1 {username}  staff   748B  8 29 17:42 id_rsa.pub
-rw-------  1 {username}  staff   3.2K  8 29 14:31 id_rsa_github
-rw-r--r--  1 {username}  staff   748B  8 29 14:31 id_rsa_github.pub
-rw-------  1 {username}  staff   1.6K  8 30 15:32 id_rsa_gitlab
-rw-r--r--  1 {username}  staff   397B  8 30 15:32 id_rsa_gitlab.pub
-rw-r--r--@ 1 {username}  staff   1.5K  9  2 10:06 known_hosts
```

## 2.生成 SSH key

ssh-keygen -t rsa -C "xxx@xx.com"

输入上述命令，点击回车后，会出现以下提示

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/{username}/.ssh/id_rsa):

这里是提示你生成的ssh key 的存储路径和名称；如果你是简单的配置一个账号，直接回车，自动取默认路径和名称。如何你是为多个git账号配置私钥/公钥，你需要自己指定路径和名称，笔者用的是id_rsa_oschina,名称可自己定义，具体如下：

Generating public/private rsa key pair.  
Enter file in which to save the key (/Users/{username}/.ssh/id_rsa): /Users/{username}/.ssh/id_rsa_oschina
继续回车

Enter passphrase (empty for no passphrase):
Enter same passphrase again:
这里取默认值空,回车即可，最后出现如下图的回显，就说明你配置成功了

Your identification has been saved in /Users/{username}/.ssh/id_rsa_oschina.
Your public key has been saved in /Users/{username}/.ssh/id_rsa_oschina.pub.
The key fingerprint is:
SHA256:lEmncZqtuXuHgZ4XtkVMkazLaTC5XgN0VLjYi3T8Fk8 xxx@xxx.com
The key s randomart image is:
+---[RSA 2048]----+
|        o o..=+o |
|       . @. + o X|
|        B..B o   |
|       . oB B . E|
|        So X = + |
|        ..* X o .|
|       ..+ O o   |
|        o.* .    |
|        .o .     |
+----[SHA256]-----+

## 3.将生成的公钥拷贝到剪贴板上，到git管理页面贴入即可
pbcopy < ~/.ssh/id_rsa_oschina.pub
id_rsa_oschina.pub的内容就是key的内容，而title的内容可以随便写~~

至此是否已经配置完成，答案否
## 4.如果你在github等上面有两个账号，可能就会导致git提交失败，那么这里就需要配置~/.ssh/config这个文件

打开config文件

open ~/.ssh/config
在文件中添加如下

#oschina
Host oschina  (名称自定义)
  HostName git.oschina.net  (服务器地址)
  user git   （`git`@git.oschina.net 与 这里`git`名称是一致的，也可自定义，但不建议修改）
  IdentityFile ~/.ssh/id_rsa_oschina (密钥存储路径)
QA
Q: 按照上述步骤执行了，仍然Permission denied (publickey)

试如下方案：

清空~/.ssh/known_hosts文件
执行ssh-add -D（删除所有）, 再次执行ssh-add -A（添加所有）
Q: 此次配置成功后，待下次重新启动电脑后，git提交又提示Permission denied (publickey)

每次重新执行ssh-add -A
每次重新执行嫌麻烦 ,ssh-add -A -K,添加到钥匙串内，这样下次如果没有知道密钥，那么则会自动取钥匙中存取的密钥
Q: 报错如下：

remote: Permission to username1/xxxx.git denied to username2.
fatal: unable to access 'https://github.com/username1/xxxx.git/': The requested URL returned error: 403
原因可能是你有两个github账户， 以前使用一个登陆并管理代码，切换另一个账号管理代码时， 钥匙串存储仍然是第一个用户的密码
执行git credential-osxkeychain erase


转载链接：[https://www.jianshu.com/p/fbbf6efb50ba](https://www.jianshu.com/p/fbbf6efb50ba)
