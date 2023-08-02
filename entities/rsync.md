---
title: rsync
created: 2023-07-28
date: 2023-08-01
tags:
- entity
- dev-ops
---
## 用法

### 基本复制

```bash
rsync -a /source/path /dest/path # 将 src 同步至 dest
rsync -a /source1/path /source2/path /dest/path # 将多个 src 同步至 dest
-a # 递归同步，并同步元信息（如修改时间、权限），更为常用
-r # 递归同步，不同步元信息
-n # 模拟执行结果
-v # 将执行结果输出至终端
-z # 同步时压缩数据
--delete # 默认情况下，rsync 只同步，而不检查目录是否相同，该参数可以使得目标目录成为原目录的镜像副本，数据删除只发生在目标目录
--append-verify # 断点续传并校验 
```

### 排除文件

```bash
--exclude # 可以指定排除的模式
# 过滤所有 txt 文件
rsync -av --exclude='*.txt' /source/path /dest/path 
# 由于 rsync 也会同步 . 开头的隐藏文件，可以如下排除隐藏文件
rsync -av --exclude='*.' /source/path /dest/path 
# 同时使用多个排除模式
# 还可以使用 --exclude={'file1.txt','dir1/*'}
# 以及文件规则 --exclude-from='exclude-file.txt'
rsync -av --exclude='file1.txt' --exclude='dir1/*' /source/path /dest/path 
--incloude # 必须包含的文件，和 --exclude 组合使用
# 排除除 txt 文件以外的所有文件
rsync -av --include="*.txt" --exclude='*' /source/path /dest/path 
```

### 远程同步

```bash
# 使用 ssh 协议同步, -e ssh 可以忽略
rsync -av -e ssh /source/path user@remote:/dest/path
rsync -av user@remote:/source/path user@remote:/dest/path
# 额外附加 ssh 参数，-e ssh 不可忽略
rsync -av 'ssh -p 2234' /source/path user@remote:/dest/path
# rsync 协议
rsync -av /source/path 192.168.ab.cd::module/dest/path
```

### 增量备份

```bash
# 指定同步时的基准目录
rsync -a --delete --link-dest /compare/path /source/path /dest/path 
--link-dest # 指定比较基准目录 /compare/path，/source/path 与 /compare/path 比较后，将内容复制到 /dest/path 
```

一个备份用户主目录的脚本实例
每一次同步都会生成一个新目录 `${BACKUP_DIR}/${DATETIME}`，并将软链接 `${BACKUP_DIR}/latest` 指向这个目录。下一次备份时，就将 `${BACKUP_DIR}/latest` 作为基准目录，生成新的备份目录。最后，再将软链接 `${BACKUP_DIR}/latest` 指向新的备份目录。

```bash
#!/bin/bash

# A script to perform incremental backups using rsync

set -o errexit
set -o nounset
set -o pipefail

readonly SOURCE_DIR="${HOME}"
readonly BACKUP_DIR="/mnt/data/backups"
readonly DATETIME="$(date '+%Y-%m-%d_%H:%M:%S')"
readonly BACKUP_PATH="${BACKUP_DIR}/${DATETIME}"
readonly LATEST_LINK="${BACKUP_DIR}/latest"

mkdir -p "${BACKUP_DIR}"

rsync -av --delete \
  "${SOURCE_DIR}/" \
  --link-dest "${LATEST_LINK}" \
  --exclude=".cache" \
  "${BACKUP_PATH}"

rm -rf "${LATEST_LINK}"
ln -s "${BACKUP_PATH}" "${LATEST_LINK}"
```

## References

- [rsync 用法教程](https://www.ruanyifeng.com/blog/2020/08/rsync.html)