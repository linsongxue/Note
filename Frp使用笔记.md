# Frp

# Frp后台自动启动的几个方法

https://blog.csdn.net/x7418520/article/details/81077652



# 用frp实现内网穿透

https://zhuanlan.zhihu.com/p/434621344

# frp官方文档

https://gofrp.org/docs/

开启服务器端frps/客户端frpc服务

```shell
frps -c frps.ini 
frpc -c frpc.ini
```

##### 

###### 方便重启frp:

```shell
netstat -nap | grep 端口号 

kill -9 进程号
```



###### 通过tcp 使用python传数据用作测试

```shell
python3 -m http.server 端口号
```

###### 使用systemctl实现自启动 

```shell
~/frp# sudo vim /lib/systemd/system/frps.service ~/frp/frp37# systemctl daemon-reload
~/frp/frp37# sudo systemctl start frps
~/frp/frp37# sudo systemctl enable frps
```



###### screen 把进程转入后台 

```shell
screen -R name 新建一个screen

sceen -ls 查看后台

Ctrl  + a -> D 把当前前台服务转到后台

Ctrl + a -> [ 可以移动光标
```



###### screen -r name 返回前台 若出现问题

1）screen 争用 ，screen 已经在其他窗口打开 

![1639221847861](C:\Users\周家名\AppData\Roaming\Typora\typora-user-images\1639221847861.png)

2) 关闭不当 sreen -r name -d 强制取消其他的同名screen