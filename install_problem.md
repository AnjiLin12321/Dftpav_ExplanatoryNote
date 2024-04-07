1 先安装 protobuf
rosdepc不可用 且很多报错是由于protobuf没有安装
protobuf其他安装方法不可用，使用压缩包安装
https://blog.csdn.net/Ftine233/article/details/131668063

2 软链接 或者 usr/local/lib加入链接库
https://blog.csdn.net/jacke121/article/details/107120650
https://blog.csdn.net/axibala5/article/details/127941267

3 'module' object has no attribute 'RAW_OPT'
https://stackoverflow.com/questions/77642155/attributeerror-module-object-has-no-attribute-raw-opt

4 ompl安装+连接
sudo apt install ros-melodic-ompl
应该不用源码安装，本机已源码安装，未验证只用命令行安装效果
https://blog.csdn.net/qq_41035283/article/details/120549554
