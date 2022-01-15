# 惠普战66锐龙版驱动

*note* 无线网卡型号是WCN685X

```
# lspci -nnvv -s 02:00.0
02:00.0 Network controller [0280]: Qualcomm Device [17cb:1103] (rev 01)
	Subsystem: Foxconn International, Inc. Device [105b:e0bb]
```


- 在debian 11上进行的操作

  1 编译可以正常驱动WCN685X的内核驱动模块ath11k.ko ath11k_pci.ko

```
# curl -O http://mirrors.ustc.edu.cn/kernel.org/linux/kernel/v5.x/linux-5.16.tar.xz

# tar xf linux-5.16.tar.xz

# rm -rf linux-5.16/drivers/net/wireless/ath/ath11k

# curl -O https://git.kernel.org/pub/scm/linux/kernel/git/kvalo/ath.git/snapshot/ath-69a6130e6ab00306fe1552c73eb0d7a0dd4b4c04.tar.gz #ath11k最新的驱动

# cp -r ath-69a6130e6ab00306fe1552c73eb0d7a0dd4b4c04/drivers/net/wireless/ath/ath11k linux-5.16/drivers/net/wireless/ath/

# cd linux-5.16

# cp /boot/config-5.10.0-10-amd64 .config

# make menuconfig

# sed -e -i "s@debian/certs/debian-uefi-certs.pem@@g" .config #注意手动打开 CONFIG_I2C_HID_ACPI=m CONFIG_I2C_HID_CORE=m配置，因为触摸板需要它，之前5.10默认是打开的，5.16进行了配置细化，需要手动开启

# make -j16 bzImage modules_install

# cp arch/x86_64/boot/bzImage /boot/vmlinuz-5.16.0

# mkinitramfs -o /boot/initramfs-5.16.0 5.16.0

```

  2 替换/lib/firmware

```

# curl -O https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/snapshot/linux-firmware-13dca280f76009ba2c5f25408543a1aaaa062c25.tar.gz #最新的linux firmware

# tar xf linux-firmware-13dca280f76009ba2c5f25408543a1aaaa062c25.tar.gz

# mv /lib/firmware /lib/firmware.old

# cp -r linux-firmware-13dca280f76009ba2c5f25408543a1aaaa062c25 /lib/firmware

# git clone https://github.com/kvalo/ath11k-firmware.git

# cp -r ath11k-firmware/* /lib/firmware/ath11k/

# cp /lib/firmware/ath11k/WCN6855/hw2.0/1.1/WLAN.HSP.1.1-03003-QCAHSPSWPL_V1_V2_SILICONZ_LITE-2/amss.bin /lib/firmware/ath11k/WCN6855/hw2.0/amss.bin #驱动默认会加载这个位置的二进制

# cp /lib/firmware/ath11k/WCN6855/hw2.0/1.1/WLAN.HSP.1.1-03003-QCAHSPSWPL_V1_V2_SILICONZ_LITE-2/m3.bin /lib/firmware/ath11k/WCN6855/hw2.0/m3.bin

```

直接启动新内核，就可以了。
