# CentOS 常用命令

#### 查看内存使用情况
```
free -m
```
#### 用以下命令清理内存
```
echo 1 > /proc/sys/vm/drop_caches
```

#### CentOS Bash/VI 快捷键(或快捷操作)
```
Ctrl + A    切换到命令行开始
Ctrl + E    切换到命令行末尾
Ctrl + I    相当于clear命令，用于清屏
Ctrl + U    剪切光标之前的内容
Ctrl + K    剪切光标之后的内容
Ctrl + Y    粘贴之前剪切的内容
Ctrl + R    在历史命令中查找命令
Ctrl + Z    程序转入后台运行，当前用户退出后终止
Ctrl + D    退出shell，即：logout，相当于执行exit
history     按编号显示你所有执行过的历史命令
!!          重复执行最后一条命令
!$          显示系统最近的一条参数
注：!$这个命令比较有用，比如我先用cat /etc/sysconfig/iptables，然后我想用vim 编辑，
可以用vim !$来代替。
```
