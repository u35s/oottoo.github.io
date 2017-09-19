# 制作自己的linux 

* 编译linux内核 
* 制作init ramfs

```
mkdir -p $1/{bin,dev} 
cd $1/bin
cp /bin/busybox .
"$PWD/busybox" --install .
cd ..
cp -a /dev/{null,tty,zero,console} dev
printf '%s\n' "#! /bin/sh" "exec /bin/sh" > init
chmod +x init
find . | cpio -oHnewc | gzip > ../initramfs.gz
cd ..
```
* qemu 启动自己的系统

```
qemu-system-x86_64 -kernel bzImage -initrd initramfs.gz
```
