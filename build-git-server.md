# 目标

在腾讯云租用了一个Linux服务器，准备安装一个版本控制的软件来管理代码。

git是一种强大的分布式版本控制软件，尤其在分支处理上甩SVN几条街。为了充分利用服务器，我准备搭建一个git服务器，用来作为协调项目成员的一个中转站。

---

# 0.服务端和客户端安装git，ssh
```
sudo apt-get install git
sudo apt-get install ssh
```

---

# 1.服务器创建一个用户

我创建一个用户名为server的用户

> sudo adduser server


![创建一个用户](http://upload-images.jianshu.io/upload_images/82327-1ba3d713ddb5ec10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
> 管理员帐户不需要加sudo

---

# 2.在服务器server用户文件夹配置信息

在server用户文件夹中，创建.ssh文件夹

> mkdir .ssh

在.ssh中touch authorized_keys文件

> touch .ssh/authorized_keys

 
![Uploading Paste_Image_402104.png . . .]
 
---

# 3.用户生成key

这一步既可以在服务器端直接生成，也可以在客户端生成。不管在哪里生成，只要能得到两个文件即可：

>私钥
公钥

下面以在客户端生成为例：

在客户端打开Git Bash，执行：

> ssh-keygen -t rsa

之后会要求输入一个用户名，我输入的是Mike。后面的直接按回车即可。


![生成用户ssh key](http://upload-images.jianshu.io/upload_images/82327-6c1301ef23c06da9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 
完成后，会生成2个文件： Mike和Mike.pub，分别是私钥和公钥


![生成的私钥和公钥](http://upload-images.jianshu.io/upload_images/82327-4859d448ec0bc95a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
---

# 4.客户端将私钥放入客户端的工作目录下

查看当前客户端工作目录：

> cd ~
pwd


 
将Mike文件放入该路径（我的是C;/Users/KKDes/）下的.ssh文件夹中；如果是第一次搭建，还要新建一个config的文件，并写入以下内容：

```
host git-server 
	user server
	hostname 119.29.147.xxx
	port 22 
	identityfile ~/.ssh/Mike
```

+ 注意除第一行，其余要缩进一个tab

+ 这里的Mike替换为自己之前创建key时输入的用户名

+ hostname 后面替换为你的服务器IP地址

---

# 5.服务器将公钥追加到服务器的authorized_keys文件中

> vim authorized_keys

![编辑authorized_keys文件](http://upload-images.jianshu.io/upload_images/82327-21fb387bbd3648a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


将公钥的内容追加到此文件中


![追加公钥到authorized_keys文件中](http://upload-images.jianshu.io/upload_images/82327-02538cea36fff4dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 

---

# 6.服务器初始化一个bare的git仓库

在服务器server用户的文件夹下创建一个文件夹repo（名字任意）用以存放代码仓库，进入此文件及，开始创建bare的git仓库

> git init --bare test.git

![在服务器中初始化一个bare的git仓库](http://upload-images.jianshu.io/upload_images/82327-fdd8fa86e619c68f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
至此，服务器端的git服务器就搭建好了；接下来，要在客户端进行clone和push操作。

---

# 7. 在客户端Clone远程的代码仓库

> git clone git-server:/home/server/repo/test.git

![从服务器clone到本地](http://upload-images.jianshu.io/upload_images/82327-7a160d47bf7b4d6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
+ git-server：表示我们在config文件配置的服务器IP地址，直接写“git-server”即可，当然，你也可以修改config文件里的名字
+ /home/server/repo/test.git：这个是远程服务器的仓库地址，按照实际情况自行修改

这样，会在gitclient/test/下创建一个名为test的文件夹（.git会被省略）。
我们可以做一个测试，在gitclient/test/test文件夹中添加一个文件，并提交。

推送到远程：

> git push git-server:/home/server/repo/test.git master

注意，这里的master代表推送到master分支，如果要推送到其他分支，修改它。

![提交更新并push到远程服务器](http://upload-images.jianshu.io/upload_images/82327-3e28fb8e347cc54d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们去其他目录再clone下来：

![在另一个目录下clone远程代码](http://upload-images.jianshu.io/upload_images/82327-079cd341908da50a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
可以看到，之前提交的文件已经可以看到了

---

# 说明

1.客户端中ssh登录，会默认读取用户目录下.ssh文件夹的config文件ssh配置信息。 

2.不论客户端还是服务器端，生成的公钥.pub应拷贝到服务器上，私key自己使用。私钥一旦泄漏，任何人都可以通过该私钥提交代码。

---

> **原创文章，未经许可，请勿转载**

> 作者：林晓州

> 日期：2016.09.26

> QQ：1139904786

> Blog：http://blog.csdn.net/kkdestiny
