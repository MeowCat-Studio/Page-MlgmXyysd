---
title: Qin 1s+ 玩机研究
date: 2019-09-14 15:29:52
id: '<%= page.date %>'
tags: 
- 玩机
- 多亲
- 随笔
- 原创
- Android
- 技术
---
## 前言
最近看见了这个多亲1s+按键机，心血来潮弄个玩玩，第三天的下午快递送来了
<!-- more -->
![](qin-1sp-research/1.jpg)
这玩意开机速度很快，让我想起了AIPC（雾），也没有开机音乐
![](qin-1sp-research/2.jpg)
比几年前的挪鸡鸭按键机开机快
![](qin-1sp-research/3.jpg)
进系统设置里看，mocor系统，还行
![](qin-1sp-research/4.jpg)
随便翻翻，有个浏览器
![](qin-1sp-research/5.jpg)
进去一看我就懵了，这特么的不就是安卓么？
![](qin-1sp-research/6.jpg)
好吧那就装个酷安耍耍#(滑稽)
连网，下载，一气呵成
点击安装包安装。。。
你特么在逗我？？？
![](qin-1sp-research/7.jpg)
行吧，这sdk是有多低啊。。
还好我保留了老版本的酷市场apk，放进sd卡，插入手机，准备安装
结果。。。
这玩意特么的连文件管理都没有？？？
## 开启USB调试
行⑧，我用adb，不过这玩意咋开启usb调试来着？设置里几个版本号按了半天也没见弹出来开发者选项啊
查了一番资料得知这手机是展讯cpu
丢上展讯工程模式代码`*#*#83781#*#*`
![](qin-1sp-research/8.jpg)
诶嘿嘿，进去了
![](qin-1sp-research/9.jpg)
翻了一下发现第二个tab有个`Allow Debug`，有些可疑，打开瞅瞅
![](qin-1sp-research/10.jpg)
再回去关于手机界面发现出来了一堆东西，点击版本号也出现了开发者选项
![](qin-1sp-research/11.jpg)
![](qin-1sp-research/12.jpg)
![](qin-1sp-research/13.jpg)
调试整开
![](qin-1sp-research/14.jpg)
![](qin-1sp-research/15.jpg)
通知栏出来了调试的图标
![](qin-1sp-research/16.jpg)
看这图标。。特么的KitKat？怪不得装不上
看一下设备列表
![](qin-1sp-research/17.png)
wtf？
![](qin-1sp-research/18.png)
原来Windows没有自带展讯机子的驱动，还得自己找
## 驱动安装
找了半天之后，点击安装，吃口月饼，差点喷出来
![](qin-1sp-research/19.png)
全 员 失 败
看安装日志发现是catalog签名问题
![](qin-1sp-research/20.png)
那就好办了，把驱动签名禁用就行
进入`设置--更新和安全--恢复--高级启动--立即重新启动`
![](qin-1sp-research/21.png)
然后点`疑难解答--高级选项--启动设置--重启`
![](qin-1sp-research/22.jpg)
![](qin-1sp-research/23.jpg)
![](qin-1sp-research/24.jpg)
![](qin-1sp-research/25.jpg)
按下F7或者7
![](qin-1sp-research/26.jpg)
然后进入系统，重新安装驱动，弹出来了确认框
![](qin-1sp-research/27.png)
安装成功
![](qin-1sp-research/28.png)
再次查看设备列表，已经显示出来了
![](qin-1sp-research/29.png)
安装应用
![](qin-1sp-research/30.png)
我屮艸芔茻
`INSTALL_FAILED_APK_RESTRICTED`
系统里某个软件拒绝了安装
看来想安装软件还得破解系统的限制
## 失败的一次尝试
用adb把build.prop扒到本地
``` bash
$ adb pull /system/build.prop
```
通过翻阅build.prop，找到了一处可疑点
第72行，`ro.sys.appinstallwhitelist`
app安装白名单，应该就是这个东西了
![](qin-1sp-research/31.png)
我们给他改成false，再通过rec刷进去试试（万一rec没有签名验证岂不是美滋滋？）
![](qin-1sp-research/32.png)
写一个刷机脚本，放在`META-INF/com/google/android/updater-script`
![](qin-1sp-research/33.png)
![](qin-1sp-research/34.png)
最后压缩成zip
![](qin-1sp-research/35.png)
重启进入Recovery模式
``` bash
$ adb reboot recovery
```
![](qin-1sp-research/36.jpg)
这。。是出错了么。。
对着按键随便按了一通之后，发现按下键盘的“8”键可以显示
![](qin-1sp-research/37.jpg)
诶，这rec有sideload模式（apply update from ADB）
![](qin-1sp-research/38.jpg)
安装试试
``` bash
$ adb sideload update.zip
```
![](qin-1sp-research/39.png)
发现无论如何都连接不上设备，还是老老实实扔进sd卡吧
从sd卡安装
欧豁，有签名验证，我们要先破解下Recovery
![](qin-1sp-research/40.jpg)
## 提取Recovery镜像
破解Recovery首先需要获取recovery.img，一般是在系统更新的包里
用Fiddler抓一下系统更新的包
打开Fiddler，勾选`Tools--Options--Connections--Allow remote computers to connect`
![](qin-1sp-research/41.png)
弹出对话框，大意是让你手动重启Fiddler，点击确定
![](qin-1sp-research/42.png)
然后重启Fiddler，手机连接wifi，代理填你电脑ip，端口默认填8888
``` bash
$ ipconfig
```
获取电脑ip
![](qin-1sp-research/43.png)
输入ip的时候注意把输入法切换为英文模式（通知栏显示ab），按#切换
![](qin-1sp-research/44.jpg)
然后进入系统更新
![](qin-1sp-research/45.jpg)
电脑上抓到了一条数据，点开一看
wtf？？？你的版本非法？？可还行？我机子全新刚拆封啊
![](qin-1sp-research/46.png)
修改下发送的数据，再发送到服务器，试了好多版本（1.2.1 1.1.x）都提示非法
最后试出来了1.0.6提示最新版本，但是并不给你包下载地址
![](qin-1sp-research/47.png)
1.0.4是最后一个给你下载地址（1.0.6）的版本
![](qin-1sp-research/48.png)
下载试试。。。只有几M可还行
看来通过抓包的方式是抓不到全量包了，不过我们知道了多亲ota服务器的目录结构
通过一顿操作成功获取到了全量包，不过是1.0.4版本的，但是这并不影响，因为我们还获取到了1.0.4版本以后的ota，只需要一个一个版本更新进去就行了
（操作方法这里就先略过吧，扒了下多亲ota服务器的sql数据库）
![](qin-1sp-research/49.png)
之前查询提示1.2.1版本非法可能是个bug
看一下全量包的目录结构
```
package.zip
	md5sum       # update.zip的md5校验文件
	update.zip   
		META-INF # 刷机脚本和签名文件存放目录
		recovery # 存放recovery差分文件
		system   # system分区文件
		boot.img # boot分区镜像
		...
```
![](qin-1sp-research/50.png)
好吧并没有完整的recovery.img文件，但是有差分文件，我们可以通过boot.img来生成，因为这是1.0.4版本的系统包，我们需要先获取到最新版的boot.img
看一下ota包的结构
```
package.zip
	md5sum             # update.zip的md5校验文件
	update.zip   
		META-INF       # 刷机脚本和签名文件存放目录
		recovery       # 存放recovery差分文件
		patch          # 存放差分文件
			boot.img.p # boot.img差分文件
			...
```
![](qin-1sp-research/51.png)
我们把boot.img和boot.img.p解压出来
除此之外还需要`META-INF/com/google/android/updater-script`里的内容
找到boot.img的那一行，把前面三个参数复制下来（选中的和前面两个）
![](qin-1sp-research/52.png)
在wsl中执行指令（因为我只找到了linux的applypatch程序）
``` bash
$ applypatch boot.img boot1.img 前面的参数(boot1.img的sha1) 中间的参数(boot1.img的字节数) 选中的参数(boot.img的sha1):boot.img.p
```
![](qin-1sp-research/53.png)
只要没有在命令返回中看到`patch did not produce expected sha1`，那说明生成成功了，把boot.img和boot.img.p删除，boot1.img重命名为boot.img，对下一个版本重复以上步骤，直到最后一个版本
最终我们得到了一个最新的boot.img，接下来找到最新那个包里的`recovery/recovery-from-boot.p`，复制过来
![](qin-1sp-research/54.png)
- 注意了，这里划重点，网上好多教程都是坑人的，有些rec不需要用到`install-recovery.sh`这个文件，具体看下面

用文本编辑器打开，查看文件头
如果有`BSDIFF`字样就好整，直接执行下面的指令就行
``` bash
$ bspatch boot.img recovery.img recovery-from-boot.p
```
如果有`IMGDIFF`字样(如下)
![](qin-1sp-research/55.png)
那需要继续提取`/recovery/etc/install-recovery.sh`和`/system/etc/recovery-resource.dat`(在全量包和系统里都可以找到)
用文本编辑器打开`install-recovery.sh`
找到`applypatch`开头的一行，复制下来
![](qin-1sp-research/56.png)
把EMMC和绝对路径改成文件路径
`applypatch -b /system/etc/recovery-resource.dat EMMC:/dev/block/platform/soc/by-name/boot:9979904:821bffe9268699430aece61933229132f882f1e9 EMMC:/dev/block/platform/soc/by-name/recovery 0a13745ae8d85762a03a0cddca7feb3c1aebce58 10479616 821bffe9268699430aece61933229132f882f1e9:/system/recovery-from-boot.p`
改为
`applypatch -b recovery-resource.dat boot.img recovery.img 0a13745ae8d85762a03a0cddca7feb3c1aebce58 10479616 821bffe9268699430aece61933229132f882f1e9:recovery-from-boot.p`
因为用到了-b开关，所以我们就不能使用刚才的那个applypatch了，需要一个Android设备或者模拟器，连上电脑，推送这三个文件到`/data/local/tmp`，然后执行指令
![](qin-1sp-research/57.png)
```bash
$ adb push boot.img recovery-from-boot.p recovery-resource.dat /data/local/tmp
$ adb shell
$ cd /data/local/tmp
$ applypatch -b recovery-resource.dat boot.img recovery.img 0a13745ae8d85762a03a0cddca7feb3c1aebce58 10479616 821bffe9268699430aece61933229132f882f1e9:recovery-from-boot.p
```
如果你看到`patch boot.img: Supporting patching EMMC targets only.`(如图)，那说明你的设备不支持这么玩，需要换个设备或者模拟器
![](qin-1sp-research/58.png)
我这里是使用`腾讯手游助手`弄好的，这个模拟器开发很方便，自带root，也没有多余的软件
```bash
$ AndroidEmulator -engine VDI # AndroidEmulator是腾讯手游助手的模拟器程序，在安装目录的ui文件夹下，一定要加-enging VDI用VDI引擎启动，否则用的是AOW引擎，会炸掉
$ adb connect localhost:5555  # 腾讯手游助手的默认adb侦听端口(如下图)，等待模拟器启动完成后再连接
$ adb push boot.img recovery-from-boot.p recovery-resource.dat /data/local/tmp
$ adb shell
# cd /data/local/tmp          # 这里应该就默认给root权限了
# applypatch -b recovery-resource.dat boot.img recovery.img 0a13745ae8d85762a03a0cddca7feb3c1aebce58 10479616 821bffe9268699430aece61933229132f882f1e9:recovery-from-boot.p
# exit                        # 上一步出现"patch boot.img: now xxxxxxx"就说明生成成功了，可以退出shell了
$ adb pull /data/local/tmp/recovery.img
```
![](qin-1sp-research/59.png)
如果一切顺利，你会看到类似如下的输出，文件夹中也会多出来一个recovery.img文件，这就是我们要的recovery了，其余的文件可以删除掉了
![](qin-1sp-research/60.png)
## 破解Recovery签名验证
用`bootimg`工具解包recovery.img
```bash
$ rename recovery.img boot.img # 因为工具只认boot.img，所以重命名一下
$ bootimg --unpack-bootimg
```
一切顺利，你会看到类似如下的输出，文件夹下多出来一堆文件
![](qin-1sp-research/61.png)
文件结构大概是这样的
```
ramdisk.gz   # rec用到的一些系统文件
boot.img     # 解包之前的文件，打包时会自动删
boot-old.img # 同上，备份，可删
initrd       # ramdisk.gz的解包
	sbin     # 存放recovery elf文件的目录
	...
```
我们要修改的是`/initrd/sbin/recovery`这个文件
用IDA Pro (32-bit)打开(64-bit好像无法保存32位的elf)
等待IDA分析文件
![](qin-1sp-research/62.png)
点击左上角的`Search for text`按钮，搜索`signature`
![](qin-1sp-research/63.png)
![](qin-1sp-research/64.png)
顺着箭头往上翻，找到最近的`CMP`，看到下面有个`BEQ`，这句就是跳转代码
![](qin-1sp-research/65.png)
普及一下arm指令集
```
BNE: 标志寄存器中Z标志位不等于零时, 跳转到BNE后标签处
BEQ(D0): 标志寄存器中Z标志位等于零时, 跳转到BEQ后标签处
B(E7)：无条件跳转，一旦遇到一个 B 指令，ARM 处理器将立即跳转到给定的地址，从那里继续执行
```
我们可以把`BEQ`修改为`BNE`，但是这样如果签名验证通过，就会挂掉(官方包无法安装)，所以我们最好是修改为无条件跳转指令`B`
右键`BEQ`，切换到汇编文本窗口
![](qin-1sp-research/66.png)
找到刚才的`BEQ`
![](qin-1sp-research/67.png)
右键，勾选`Synchronize with--Hex View 1`(如已勾选请跳过)
![](qin-1sp-research/68.png)
点击`Hex View`标签栏，选中`D0`
![](qin-1sp-research/69.png)
点击左上角`Edit--Patch program--Change byte`
![](qin-1sp-research/70.png)
将`D0`改成`E7`
![](qin-1sp-research/71.png)
返回`Text View`标签栏发现`BEQ`改成了`B`
同理，再次查询`signature`，继续修改，这里不再过多叙述
![](qin-1sp-research/72.png)
点击左上角`Edit--Patch program--Apply patches to input file`
![](qin-1sp-research/73.png)
点击`OK`保存文件
![](qin-1sp-research/74.png)
最后关闭窗口，勾选`DON'T SAVE the database`后再点击`OK`
![](qin-1sp-research/75.png)
最后将更改打包进img
```bash
$ bootimg --repack-bootimg
$ rename boot-new.img recovery.img
```
![](qin-1sp-research/76.png)
## 刷入修改后的Recovery
手机连接电脑，执行指令
```bash
$ adb reboot bootloader
$ fastboot flash recovery recovery.img
```
刷入成功，机子并没有BootLoader锁
![](qin-1sp-research/77.png)
这里直接刷入了，按理说是应该先执行boot指令测试img的，但是因为是展讯的cpu，所以boot指令是废的（[参考资料](https://blog.csdn.net/bmw7bmw7/article/details/45392637)）
这里需要注意，我们并不能直接重启，因为system里还有install-recovery.sh，一重启rec就会恢复为原版
所以我们需要在install-recovery.sh执行之前让他启动到recovery里，但是adb不会wating设备，fastboot这点做的就很好
两种选择：
1. 魔改adb让他检测不到设备时循环检测不退出
2. 自己写个循环脚本

我最终选择了第二个，为什么呢？因为我存放aosp源码的linux虚拟机所在的磁盘满了，无法启动，所以无法魔改adb
话不多说直接上代码吧
```batch
@echo off
adb kill-server
adb start-server
fastboot reboot
:start
adb reboot recovery
if "%errorlevel%"=="0" (
	goto break
)
goto start
:break
exit
```
在控制台刷了一堆`error: no devices/emulators found`之后，手机黑屏重启进入了rec模式
![](qin-1sp-research/78.jpg)
正当我兴高采烈地去刷的时候，发现。。炸了。。
```
E:failed to set up expected mounts for install; aborting
Installation aborted.
```
![](qin-1sp-research/79.jpg)
多次尝试之后，发现这只是个bug，多刷几次或者拔掉数据线就好了
![](qin-1sp-research/80.jpg)
```
Install from sdcard complete.
```
大功告成，破解成功，重启进入系统安装试试水
```bash
$ adb install Coolapk.apk
```
![](qin-1sp-research/81.png)
安装成功！
![](qin-1sp-research/82.jpg)
![](qin-1sp-research/83.jpg)
## 后语
至此，破解安装应用已完成，需要注意的是每次进入rec都需要遵循以下步骤：

```bash
$ adb reboot bootloader
$ fastboot flash recovery recovery.img
```
然后执行那个循环脚本，不然进入的是官方rec
或者你可以直接把那个`install-recovery.sh`给搞掉，我懒的搞
也可以刷个`Magisk`或者`SuperSU`来root什么的，建议是最好不要直接修改system分区，因为炸了不好整
刷机包呢，你也可以使用bsdiff来让你的破解更加优雅（大雾）
## 资源下载
[驱动(请禁用驱动签名)](http://cdn.meowcat.org/mlgmxyysd/storage/201909162236/Drivers.zip)
[工具打包(不含驱动，手机打开adb模式连接电脑，运行crack.cmd)](http://cdn.meowcat.org/mlgmxyysd/storage/201909162236/Qin1sp_crack.zip)
## 版权信息
驱动来自SpreadTrum及Google
bootimg来自cofface
bsdiff&bspatch来自http://www.daemonology.net/bsdiff/
applypatch来自https://github.com/airk000/Applypatch_for_Linux
recovery.img来自多亲，由MlgmXyysd修改

----------
友情提示：玩机有风险，刷机需谨慎，由刷机带来的一切后果本人概不负责
文章由MlgmXyysd撰写，转载请注明http://mlgmxyysd.meowcat.org/
