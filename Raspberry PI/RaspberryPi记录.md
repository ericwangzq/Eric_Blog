# 设置Wi-Fi

# 宝塔Linux面板
服务器端：http://www.ericwangzq.top:8866
ericwangzq
*Eric@10080613

树莓派端：http://192.168.1.145:28888
ericwangzq
*Eric@10080613

# FRP内网穿透相关

frp穿透内网指定文件存储位置：http://128.1.134.121:6001/static/
ericwangzq
*Eric@10080613

dashboard: http://128.1.134.121:7500
ericwangzq
*Eric@10080613

# 开启摄像头模块

系统没有内置rspi-config命令，配置/boot/config.txt文件开启摄像头模块，修改如下几个配置项
```shell
##########################################
## - Enable audio (loads snd_bcm2835) - ##
##
dtparam=audio=on
########################################################################
## per https://github.com/anholt/mesa/issues/56#issuecomment-263283300
## gpu_mem is for closed-source driver only; since we are only using the
## open-source driver here, set low.
gpu_mem=256
################################
## Camera Module used: start_x=1 
start_x=1

gpu_mem：分配给摄像头模块的内存大小，最小需要128M
start_x=1：设置打开摄像头模块
```

# 用mjpg-streamer在线看视频流
```
/usr/local/bin/mjpg_streamer -i "/usr/local/lib/mjpg-streamer/input_uvc.so -n -f 30 -r 1280x720" -o "/usr/local/lib/mjpg-streamer/output_http.so -p 8080 -w /usr/local/share/mjpg-streamer/www"
```
## 遇到问题：找不到/dev/video0
```c
MJPG Streamer Version.: 2.0
 i: Using V4L2 device.: /dev/video0
 i: Desired Resolution: 640 x 480
 i: Frames Per Second.: -1
 i: Format............: JPEG
 i: TV-Norm...........: DEFAULT
ERROR opening V4L interface: No such file or directory
 Init v4L2 failed !! exit fatal 
 i: init_VideoIn failed
 ```
 这个报错，是因为使用直接插到树莓派视频CSI接口的摄像头造成的。如果使用usb摄像头，则会在/dev目录下出现相关的设备。而直接插到树莓派视频CSI接口的摄像头，使用的是树莓派中的camera module，它是放在/boot/目录下以固件的形式加载的，不是一个标准的v4l2的摄像头ko驱动，所以加载起来之后会找不到/dev/video0的设备节点，这是因为这个驱动是在底层的，v4l2这个驱动框架还没有加载，所以要在/etc/modules里面添加一行bcm2835-v4l2,这句话意思是在系统启动之后会加载这个文件中模块名，这个模块会在树莓派系统的/lib/modules/xxx/xxx/xxx下面，添加之后重启系统，就会在/dev/下面发现video0设备节点了。这个文件名可能不是叫/etc/modules，较早的版本是/etc/modules-load.d/rpi-camera.conf.

然后，重启一下。

本地访问：
http://192.168.1.145:8080/stream.html