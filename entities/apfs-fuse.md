---
title: apfs-fuse
aliases:
- apfs 工具
created: 2023-08-01
date: 2023-08-01
tags:
- entity
---
## Guide

使用 `apfs-fuse` 让 Ubuntu 挂载 APFS 硬盘

```bash
sudo apt update
# 必要前置包
sudo apt install fuse3 libfuse3-dev bzip2 libbz2-dev cmake gcc g++ git libattr1-dev zlib1g-dev
# 安装 apfs-fuse
git clone https://github.com/sgan81/apfs-fuse.git
cd apfs-fuse
git submodule init && git submodule update
mkdir build
cd build
cmake ..
make
# 将 bin 移到 usr 目录下，修改权限
sudo cp apfs* /usr/local/bin
sudo chown root:root /usr/local/bin/apfs*
# 挂载 /dev/sda2
sudo apfs-fuse -o allow_other /dev/sda2 /mnt
umount /mnt
```

## References

- [apfs-fuse](https://tutorialforlinux.com/2022/01/17/apfs-fuse-ubuntu-22-04-installation-step-by-step/)