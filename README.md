# Android APP升级劫持
劫持Android APP升级，引到用户安装恶意APP

# APK升级过程
- 服务器检查是否有升级包：https/http，返回包下载链接+包hash值
- 下载升级包：https/http
- 校验包hash值，保存SDCard：*攻击者的机会*
- 启动系统安装器

# 安全性分析
- 一般APP在保存到SDCard或启动安装器之前都会校验升级包hash值，也是最安全的做法
- 有些APP在包保存时校验hash值，然后提示用户进行升级，用户点击后启动系统安装器，这会间隔一段时间；而且从APP启动安装器到安装器就绪，中间也有一段时间
- *攻击者可以在这段时间内，安装器就绪之前替换SDCard上的升级包*，可通过FileObserve实现
- 另外系统安装器也可能存在漏洞：*检查APP展示权限详情时读取的是合法APP，但安装的确实攻击者替换过的恶意APK*

安装器漏洞参考 https://unit42.paloaltonetworks.com/android-installer-hijacking-vulnerability-could-expose-android-users-to-malware/

# Demo
- 主流APP都可以劫持，支付宝APP演示(链接: https://pan.baidu.com/s/1zsltgnpf8mKqicxDrFK2OA 提取码: xy4x)

# 缓解
- 将APK包存储在私有目录。设置其他可读权限或使用fileprovider允许读权限
- 一般使用DownloadManger进行下载，需要赋予其写权限；百度文库的做法是先下载到SDCard > 复制到私有目录 > 设置只读权限 > 校验hash> 启动安装
- FileObserve只能监控目标目录下的文件变化，使用动态的子文件夹名，常规手段即可失效。（需要不停监控子文件夹是否出现，导致监控程序可能被垃圾回收）
