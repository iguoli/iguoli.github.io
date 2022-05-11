---
title: Github API
date: 2022-05-11
modify_date: 2022-05-11
tags: Github
key: Github-API-2022-05-11
---

## 在 Organization 间迁移 Repositories

Github 提供了 [Transfer a repository](https://docs.github.com/en/rest/repos/repos#transfer-a-repository) API, 可以在个人或组织间转移仓库。

在 Github 页面个人头像上选择 *Settings* -> *Developer settings* -> *Personal access tokens*，添加一个新的访问token，作为访问 Github API 的密码。

下面的脚本可用于批量转移多个仓库，将要转移的仓库名写入 *repos.txt* 文件。

<!--more-->

```bash
#!/usr/bin/env bash

function git_repo_transfer() {
    org=$1
    new_org=$2
    repo=$3
    curl -L \
        -u "<username>:<generated access token>" \
        -H "Content-Type: application/json" \
        -H "Accept: application/vnd.github.v3+json" \
        -X POST https://github.dxc.com/api/v3/repos/$org/$repo/transfer \
        -d "{\"new_owner\":\"$new_org\"}"
}

dos2unix ./repos.txt
repos=$(cat ./repos.txt)
echo $repos

# $1 is origin organization name, $2 is new organization name
for repo in $repos; do (git_repo_transfer $1 $2 "$repo"); done
```