---
title: git submodule
created: 2023-08-02
date: 2023-08-02
tags:
- entity
---
## Guide

git submodule 使得一个 git 仓库可以从属于另一个 Git 仓库，这意味着 commit 只与自己的项目相关，而与某个依赖的子模块无关

```bash
# 添加 submodule 并将目录重命名
git submodule add https://github_repo dir-your-want
# 拉取带子模块的仓库
git clone https://github_repo_with_submodule
git submodule update --init --recursive 
# 指定子模块 branch 为需要的分支
git config -f .gitmodules submodule.submodule_name.branch branch_name
# 删除子模块
git rm --cached submodule_name
# 删除 .gitmodules 中子模块信息
# 删除 .git/config 中子模块信息
# 删除子模块
```

## References

[Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)