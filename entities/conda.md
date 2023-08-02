---
title: conda
aliases:
  - "Python 环境管理器"
created: 2023-07-27
date: 2023-07-31
tags:
  - entity
  - dev-ops
---

- 基础配置

```bash
cat ~/.condarc
# 自动启动 conda 环境，设置关闭，防止系统 python 被污染
auto_activate_base: False
envs_dirs:
  - /platform_tech/zhangyin/conda_resource/envs/
pkgs_dirs:
  - /platform_tech/zhangyin/conda_resource/pkgs/
```

- 常用命令

```bash
# 创建环境
conda create -n env_name python=3.8
# 删除环境
conda env remove -n env_name
# 复制环境
conda create -n env_name --clone old_env_name
```

## References

1. [conda document](https://docs.conda.io/projects/conda/en/stable/index.html)