## 概述
检查系统二进制文件是否被rootkit病毒修改的一个shell脚本

[官网地址](http://www.chkrootkit.org/)


## 使用
```
# 无需安装，下载解压即可使用
wget ftp://ftp.pangeia.com.br/pub/seg/pac/chkrootkit.tar.gz

tar -xf chkrootkit.tar.gz

cd chkrootkit-0.52

./chkrootkit -n
```

### 参数说明
```
-h:查看帮助
-V:显示版本信息
-l:显示可测试内容
-d:debug
-q:安静模式
-x:专家模式
-r dir:使用dir目录作为根目录
-p dir1:dir2:dirN	
-n:跳过NFS挂载目录
```
