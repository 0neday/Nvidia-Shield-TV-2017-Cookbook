# Nvidia Shield TV 2017 国行刷美版固件指南

## 必备文件

1. 命令行工具[minimal_adb_fastboot_v1.4.3_setup.exe](https://forum.xda-developers.com/showthread.php?t=2317790)
2. 驱动程序[SHIELD_Family_WHQL_USB_driver_201801.zip](https://developer.nvidia.com/gameworksdownload#?search=SHIELD%20Family%20Windows%20USB)
3. 美版固件[nv-recovery-image-shield-atv-2017-7.0.1.zip](https://developer.nvidia.com/gameworksdownload#?tx=$additional,shield)

## 准备工作

1. 安装minimal_adb_fastboot_v1.4.3_setup.exe

2. 解压驱动程序SHIELD_Family_WHQL_USB_driver_201801.zip至桌面

3. 解压美版固件nv-recovery-image-shield-atv-2017-7.0.1.zip至任意文件夹，将下列文件移至minimal_adb_fastboot的安装目录

   > - blob
   > - boot.img
   > - recovery.img
   > - system.img
   > - vendor.img

4. 在断电状态下，将Nvidia Shield TV 2017接入如下设备

   > - 通过HDMI接口连接电视或显示器
   > - 通过紧挨HDMI接口的USB接口连接Shield手柄连线，并将连线的另一端接入手柄
   > - 通过靠近散热孔的USB接口连接USB 3.0公对公数据线，并将数据线的另一端留空
   > - 确保路由器可联网（此时无需翻墙）
   > - 打开电视或显示器

## 刷机过程

1. 接通Nvidia Shield TV 2017的电源，依照显示器上的提示，联网并激活设备

2. 进入“设置”-“关于”，将光标定位在“内部版本号”上，连续按7次手柄A键，此时屏幕提示“您现在已处于开发者模式！”

3. 按手柄B键后退，进入“开发者选项”，开启“USB调试”，此时屏幕提示“是否允许USB调试”，在“确定”上按手柄A键，此时屏幕提示“已启用USB调试”

4. 拔除连接至计算机的一切Android设备

5. 将USB 3.0数据线的另一端连接至电脑的USB 3.0端口，计算机会自动安装Nvidia Shield TV驱动。安装完成后，在“设备管理器”-“通用串行总线设备”中，可以看到“ADB Interface”，它的前面没有叹号

6. 使用管理员身份运行“Minimal ADB and Fastboot”，执行如下指令

   ```
   adb devices
   ```

   若看到命令提示符中最后一行为“数字+device”，则表示该设备已连接成功，可继续执行下述操作，否则，必须重新安装驱动程序

7. 执行如下指令

   ```
   adb reboot bootloader
   ```
   重启Nvidia Shield TV 2017进入bootloader界面
   
   或者在重启动的时候，同时按住手柄的AB键。

8. 此时，Nvidia Shield TV 2017重启并进入bootloader界面，此时，计算机会安装Fastboot驱动，选择从磁盘安装，找到SHIELD_Family_WHQL_USB_driver_201801.zip的解压文件夹并执行安装，屏幕提示Android ADB Interface驱动安装成功

9. 执行如下指令

   ```
   fastboot oem unlock
   ```

   > 注意，执行该指令会擦除Nvidia Shield TV 2017中的全部数据，如有必要，务必提前备份相关文件

   进入bootloader解锁提示界面，此时，按手柄A键
   
   耐心等待命令执行完毕。


10. 按次序逐一执行如下指令

    ```
    fastboot flash staging blob
    fastboot flash boot boot.img
    fastboot flash recovery recovery.img
    fastboot flash system system.img
    fastboot flash vendor vendor.img
    ```

    有几条指令需要较长时间，务必耐心等待。途中不要做任何操作


11. 执行如下指令

    ```
    fastboot oem lock
    ```

    > 注意，执行该指令会擦除Nvidia Shield TV 2017中的全部数据，如有必要，务必提前备份相关文件

    进入bootloader锁定提示界面，此时，按手柄A键
    
 12、fix time setting and dns
    ```
   # fix time server, redirect all udp 123 to local timeserver
   iptables -t nat -N TIMER
   iptables -t nat -A TIMER  -d 192.168.1.1 -j RETURN
   iptables -t nat -A TIMER  -p udp  -j REDIRECT --to-ports 123
   iptables -t nat -I PREROUTING -p udp --dport 123 -j TIMER

   # netflix dns fix
   iptables -t nat -N NETFLIX
   iptables -t nat -A NETFLIX  -d 192.168.1.1 -j RETURN
   iptables -t nat -A NETFLIX  -p udp  -j REDIRECT --to-ports 'your local dns server'
   iptables -t nat -I PREROUTING -p udp --dport 53 -j NETFLIX

    ```
 
