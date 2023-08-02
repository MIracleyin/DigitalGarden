---
title: unix tools
aliases:
- Unix 系统常用工具
created: 2023-07-26
date: 2023-08-01
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
gzip bigfile
# bzip2 类似 gzip
bzip bigfile
# xz 类似 gzip 花费更多时间，压缩率更高
xz bigfile
```

- macOS 相关

```bash
# 允许运行未签名 app
sudo xattr -r -d com.apple.quarantine /Applications/any.app
```

## References

- [在 Linux 上压缩文件的 5 种方法](https://linux.cn/article-12190-1.html)