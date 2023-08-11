---
title: unix tools
aliases:
- Unix 系统常用工具
created: 2023-07-26
date: 2023-08-11
tags:
- entity
- dev-ops
---
## Guide

基础但常用的 Unix 工具

- 磁盘

```
# 查看当前路径空间总体占用
du -sh /path/to/know
```

- 压缩、解压

```bash
# 分卷压缩
tar czf - test | split -b 1G - test.zip
# 合并 并解压
cat test.zip* > test.zip unzip
# tar
tar cfz bigfile.tgz bigfile
# zip 
zip ./bigfile.zip bigfile
# gzip 就地压缩，原文件被压缩文件替换
tar –xvf file.tar
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2tar –xZvf file.tar.Z //解压tar.Z

gzip bigfile
# bzip2 类似 gzip
bzip bigfile
# xz 类似 gzip 花费更多时间，压缩率更高
xz bigfile
```

- 批量修改后缀名

```bash
for i in /path/to/sample/*.a; do mv "$i" "${i%.a}.b"; done
```

- 查看路径下文件数量

```bash
ls -l |grep "^-"|wc -l
# 查看行数 
wc -l file
```

- macOS 相关

```bash
# 允许运行未签名 app
sudo xattr -r -d com.apple.quarantine /Applications/any.app
```

## References

- [在 Linux 上压缩文件的 5 种方法](https://linux.cn/article-12190-1.html)