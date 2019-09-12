分区
mtd0: 00000000 00100000 "bootloader"
mtd1: 00100000 00200000 "params"
mtd2: 00200000 00a00000 "kernel"
mtd3: 00a00000 00b00000 "dtb"
mtd4: 0b000000 -------- "ubifs"

1 通过dd 从mtd读取各自的镜像保存，注意，如果内核与文件系统中的mpm相关模块不匹配，会导致mpm启动不正常
dtb.bin  kernel.bin  param.bin  uboot.bin  

2 u-boot-ccsload.bin  DDRA、DDRB均初始化的ccs镜像，u-boot-ddra.bin至初始化DDRB的镜像，通过ccs load memory到0xc000000，然后复位cpu，ctrl+F8 启动

3 uboot的烧写，加载kernel等镜像到0x60000000内存空间，通过u-boot-ddra.bin启动，nand erase.part nand write 烧写

4 文件系统的烧写，加载ubifs文件系统镜像到0x60000000内存空间，通过u-boot-ddra.bin启动，nand erase.part， ubi part ubifs, ubi create.vol k2hk-evm-rootfs, ubi write.vol 0x60000000 k2hk-evm-rootfs 文件大小烧写

5 从原有系统copy /rtframe /bin/dsp-wrapper /bin/g*m /usr/bin/mpm* 至当前系统

scp -r root@10.10.10.4:/lib/modules/ /lib
scp -r root@10.10.10.4:/rtframe /
修改start脚本

scp -r root@10.10.10.4:/bin/dsp-wrapper /bin/
scp -r root@10.10.10.4:/bin/g*m /bin
scp -r root@10.10.10.4:/usr/bin/mpm* /usr/bin/
scp root@10.10.10.4:/lib/dsp_executor.so /lib
scp root@10.10.10.4:/lib/systemd/system/rtframe-daemon.service /lib/systemd/system/
systemctl enable rtframe-daemon.service

6 修改启动文件启动客户端

