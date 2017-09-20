# 制作自己的linux 

* 编译linux内核 
* 制作基于busybox init的init ramfs

```
mkdir -p $1/{bin,dev,proc,sys,etc} 
mknod -m 600 $1/dev/console c 5 1
mknod -m 600 $1/dev/null c 1 2
mknod -m 600 $1/dev/tty c 4 1
mknod -m 600 $1/dev/tty1 c 4 1
cd $1/bin
cp /bin/busybox .
"$PWD/busybox" --install .
cp linuxrc ../init
cd ..
cd etc
printf '%s\n' "::askfirst:/bin/sh" > inittab
cd ..
find . | cpio -oHnewc | gzip > ../initramfs.gz
cd ..
```
* qemu 启动自己的系统

```
qemu-system-x86_64 -kernel bzImage -initrd initramfs.gz
```
