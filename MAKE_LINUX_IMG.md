# 制作自己的linux 

* 编译linux内核 
* 制作基于busybox init的init ramfs

```
mkdir -p $1/{bin,dev,proc,sys,etc,etc/init.d} 
mknod -m 600 $1/dev/console c 5 1
mknod -m 600 $1/dev/null c 1 2
mknod -m 600 $1/dev/tty c 4 1
mknod -m 600 $1/dev/tty1 c 4 1
cp /bin/busybox $1/bin/
$1/bin/busybox --install $1/bin
cp $1/bin/linuxrc $1//init
printf '%s\n' "::sysinit:/etc/init.d/rcS" "::askfirst:/bin/sh" > $1/etc/inittab
printf '%s\n' "#!/bin/sh" "mount -t proc proc /proc" "mount -t sysfs sysfs /sys" "mdev -s" > $1/etc/init.d/rcS
chmod +x $1/etc/init.d/rcS
touch $1/etc/mdev.conf
cd $1
find . | cpio -oHnewc | gzip > ../initramfs.gz
```
* qemu 启动自己的系统

```
qemu-system-x86_64 -kernel bzImage -initrd initramfs.gz
```
