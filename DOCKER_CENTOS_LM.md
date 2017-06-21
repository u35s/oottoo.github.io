### docker centos 中文乱码

```
yum -y install kde-l10n-Chinese && yum -y reinstall glibc-common
localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
export LC_ALL=zh_CN.utf8
```
