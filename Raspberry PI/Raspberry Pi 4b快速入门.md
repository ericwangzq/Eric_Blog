Raspberry Pi®是[Raspberry Pi Foundation](http://www.raspberrypi.org/)基于**ARM**制造的信用卡大小的SBC（单板计算机）。树莓派运行基于Debian的GNU/Linux操作系统Raspbian，并且具有很多其他系统上的端口。

Raspberry Pi 4 B是最新版本，可以在[这里](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/)查看介绍。

![Raspberry Pi 4b](https://raw.githubusercontent.com/ericwangzq/Eric_Blog/master/assets/20200504163646.png)

# 镜像安装

系统的安装依然要用SD卡，先将系统镜像烧录进SD卡，后插到树莓派上，通电即可启动并运行。
可以在[这里](https://www.raspberrypi.org/downloads/raspbian/)下载所需要的系统固件。

## 镜像烧录
这里将介绍用MacOS对SD卡进行烧录：
- 将Tf卡插入SD卡读卡器，并插入Mac电脑。SD卡容量建议不小于16G。
- 在这里下载[Etcher](https://etcher.io/)——SD卡烧录软件，下载完成后安装并打开。
![Etcher](https://raw.githubusercontent.com/ericwangzq/Eric_Blog/master/assets/20200504170354.png)
- 依照指引选择镜像文件，再选择目标SD卡，最后点击Flash，等待几分钟即完成了烧录。

## 开启SSH
烧录完毕的SD卡将会挂在~/Volumes下的boot分区，在Mac terminal下进入boot分区后新建**ssh**的空白文件（注意小写，且不能包含扩展名），即完成了SSH开启。
```shell
$cd ~/Volumes/boot
$touch ssh
```

## WiFi配置
在未启动树莓派的状态下单独修改`/boot/wpa_supplicant.conf`文件配置WiFi的SSID和密码，这样树莓派启动后会自行读取wpa_supplicant.conf配置文件连接WiFi设备。
```c
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
ssid="WiFi名称"
psk="WiFi密码"
key_mgmt=WPA-PSK
priority=1
}
```
最后将SD卡插入树莓派，并进行供电，便可以开始使用。

# 初始化配置

## SSH连接
找到树莓派在局域网下的IP地址（可以通过路由器的设备列表获得，也可以通过`arp -a`查看局域网下全部IP地址）

用ssh连接树莓派，默认用户名和密码是：
```shell
login as: pi
pi@192.168.1.145's password:raspberry

$ssh pi@192.169.1.145
```
未来如果遇到`Host key verification failed`的问题，可以通过修改`~/.ssh/known_hosts`里的hosts来重新验证。
> 这个问题通常是由于访问使用的公钥与服务器记录的差异引起的。ssh服务会把每个曾经访问过计算机或服务器的公钥（public key），记录在~/.ssh/known_hosts。当下次访问曾经访问过的计算机或服务器时，ssh就会核对公钥，如果和上次记录的不同，OpenSSH会发出警告。

## VNC服务
VNC可以远程显示树莓派桌面，在ssh连接到终端后进行如下操作。

```shell
$sudo raspi-config
```
- 5: Interfacing Options
- P3: VNC
- YES

即完成了树莓派上VNC服务的开启。

下载[Real VNC](https://www.baidu.com/link?url=iW6RVDaZdafJwUX4boQhLuh7MNRw4HkAi4QgoqmnfDXU4bT46q_bCJmDjLUpERyvWlFnof0B4D4VDeaZYD51Ea&wd=&eqid=a185411b00045afd000000065d4beff3)，安装并打开后，输入树莓派的IP地址即可访问。

## 更换国内软件源
换源的目的是提高树莓派系统软件的安装(`apt-get`)速度，首先要确定系统版本：
```shell
$lsb-release -a
...
Codename:   buster
```
编辑/etc/apt/sources.list文件，用下面的地址进行替换。
编辑/etc/apt/sources.list.d/raspi.list文件，用下面的地址进行替换。

完成后，`sudo apt-get update`更新软件源列表，`sudo apt-get upgrade` 更新软件。

### 国内软件源列表(buster)

**/etc/apt/sources.list**
```shell
#清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi

#中国科学技术大学
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contribnon-free rpi

#阿里云
deb http://mirrors.aliyun.com/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ buster main contrib non-free rpi

#华中科技大学
deb http://mirrors.hustunique.com/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.hustunique.com/raspbian/raspbian/ buster main contrib non-free rpi
```

**/etc/apt/sources.list.d/raspi.list**
```
#清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/archive.raspberrypi.org/debian/ buster main ui

#中国科学技术大学
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ buster main ui

#阿里云
deb http://mirrors.aliyun.com/archive.raspberrypi.org/debian/ buster main ui

#华中科技大学
deb http://mirrors.hustunique.com/archive.raspberrypi.org/debian/ buster main ui
```