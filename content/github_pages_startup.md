Date: 2015-07-22
Title: 如何搭建github博客
Category: gentoo

##Github帐户相关
* 一定要作邮箱验证,否则github pages无法访问;且不能使用163之类的邮箱,我注册了hotmail邮箱才收到验证邮件
* git访问要通过ssh验证,通过ssh-keygen产生PC上的ssh keys,然后添加到github的ssh keys栏中即可
* git config设置git用户,邮箱等信息
* 如果git push依然不成功,可以使用http协议来clone代码和提交内容
* 新建repositories,自动新建github pages,新建一个source branch,用来存储md文件


##建立gentoo环境

* emerge git,pelican,extext工具,解决依赖关系
* pelican-quickstart建立基本博客环境
>myblog

>├── content # 存放输入的源文件

>│ └── (pages) # 存放手工创建的静态页面

>├── output # 生成的输出文件

>├── develop_server.sh # 方便开启测试服务器

>├── Makefile # 方便管理博客的Makefile

>├── pelicanconf.py # 主配置文件

>└── publishconf.py # 发布时使用的配置文件

* 使用extext写好的md文档放入content目录,执行make html
* make devserver执行成功后可以在http://localhost:8000查看博客
* ./develop_server.sh stop停止浏览功能


##提交静态网页
* git clone在github上的代码,git rm . -rf
* 将pelican产生的output目录下的内容mv到git目录下
* git add .;git commit -am "first commit";git push
* 在github中将main分之切换成source分之,将除了output之外的其他内容cp到该git目录下并提交


##问题反馈之联系方式
* 邮件(smallhome@hotmail.com)
* 博客: smallhome.github.io/blog
