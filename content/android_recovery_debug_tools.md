Date: 2015-07-25
Title: recovery调试方法
Category: android

##问题的来由
recovery里面调试不能进shell,抓取log只有当recovery执行完成退出时才能保存下来。我们现在要达到两个目标:

1.即使recovery没有执行完成,依然可以保存下来log信息

2.添加adb shell环境,方便使用android提供的一些调试工具


##修改log机制
recovery程序从boogloader/recovery下的程序生成的,在recovery的main函数里,将标准输出,标准出错输出通过freopen

重定向到了/tmp/recovery.log,只有当程序执行结束时会将文件拷贝到cache分区

1.修改标准输出,标准出错输出重定向到/cache/recovery.log文件;

2.在recovery里面,挂在文件系统是在程序执行过程中而不是在fstab中,所以重定向到cache中的文件前,先挂载cache分区

3.通过freopen重定向时,mode参数传入"a",则将log加入到log文件末尾;如果传入"w",则将log清楚掉重新从文件头开始写入;

4.当系统卡死时通过烧写misc.img可以快速进入ftm并获取root权限(进入ftm模式时我们添加了root权限)

扩展:我们可以将以上方法扩展开来,在第一个用户程序init中将cache分区挂载上,然后重定向标准输出,标准出错到/cache/startup.log中,还可以根据

ftm,recovery,charge下的不同属性,来作一些特殊的抓取log需求:抓取第一次刷完版本log,recovery升完级的log,第一次恢复出场设置log等

##搭建adb shell环境
* 添加adb shell功能

1.挂在system分区

2.设置PATH环境变量添加/system/bin和/system/xbin

3.启动adbd,且设置为正常模式而不是recovery模式

4.设置usb vid,pid,usb config等属性,并设置enable为1

在恢复出场设置或者升级过程中和升级系统时,需要卸载掉system,否则将无法恢复出场设置或者升级;所以我们需要进一步提出另外的方案

* 不改变分区方案的方法

1.交叉编译busybox和sh,strace等工具,选择静态链接的方式编译,直接将需要的程序放在/sbin下;修改PATH路径

2.修改adbd的service文件,将调用sh的路径指向/sbin/sh

* 添加boot和recovery分区大小

1.我们可以扩大root和recovery分区的大小,在其中建立system/bin和system/lib目录,然后将需要的lib和bin文件拷贝到对应文件夹下,其中linker和sh

是必须包含的

2.挂在system分区时,会将/system里面的内容覆盖掉;如果不将我们的环境放在/system目录,除了修改PATH变量和sh路径外,还需要修改linker动态库链接程

序中动态库存放的目录

##问题反馈之联系方式
* 邮件(wangxiaohua1990@hotmail.com)
* 技术博客: smallhome.github.io/blog
