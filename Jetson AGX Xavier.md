# Jetson AGX Xavier

## 刷机

1. 从软件中心https://developer.nvidia.com/embedded/downloads#?search=NVIDIA%20SDK%20Manager下载一个SDK，安装到Linux主机上

2. 将AGX与Linux主机相连

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1h8ngv96proj215b0kmdsn.jpg)

* hostmachine 可以不勾选，不在宿主机机型部署；

* target Hardware 必须勾选且可以检测到agx，如果出现no board connected，按下agx中间复位键不松开、按下左侧电源键不松开，两秒后同时松开。点集refresh，会检测到agx设备，如未检测到，重复上述操作。

* 关于jetpack版本，推荐不要选择最新的版本。

3. Jetson OS安装完后需要先完成用户名密码之类的设置，再安装component

## 将外置SD卡/固态硬盘挂载

由于AGX的本身存储不大，因此要带个大容量SD卡或者固态硬盘

只物理装上的话使用

```bash
df -h
```

是查不到的，所以首先用

```bash
sudo fdisk -l
```

找到需要的盘，我的是

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1h8nh7qz7r4j20v6046q4h.jpg)

这种盘直接挂载会出问题，首先要使用

```bash
sudo mkfs -t ext4 /dev/nvme0n1
```

格式化，然后将盘挂载到自己的目标目录上，例如我把其挂载到我``/home/its/workplace/``路径下

```bash
sudo mount /dev/nvme0n1 /home/its/workplace/
```

为了防止每次开机都手动加载硬盘，可以写入``/etc/fstab``文件下

![image.png](http://tva1.sinaimg.cn/large/70b5161bgy1h8nhd8zol1j218a0820yx.jpg)

重新挂载

```bash
sudo mount -a
```

然后就可以了

## 打开风扇

```bash
echo 255 | sudo tee /sys/devices/pwm-fan/target-pwm
```

255是自定值，0-255均可，0代表关闭，255代表最大