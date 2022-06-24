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

下面的脚本可用于批量转移多个仓库。

<!--more-->

```sh
#!/usr/bin/env bash
#
# script name: transfer-repo.sh

function git_repo_transfer {
    org=$1
    new_org=$2
    repo=$3
    curl -L \
        -u "<username>:<generated access token>" \
        -H "Content-Type: application/json" \
        -H "Accept: application/vnd.github.v3+json" \
        -X POST https://github.company.com/api/v3/repos/$org/$repo/transfer \
        -d "{\"new_owner\":\"$new_org\"}"
}

dos2unix ./repos.txt
repos=$(cat ./repos.txt)
echo $repos

# $1 is origin organization name, $2 is new organization name
for repo in $repos; do (git_repo_transfer $1 $2 "$repo"); done
```

将要转移的仓库名写入 *repos.txt* 文件，一行一个，然后在命令行执行脚本

```sh
./transfer-repo.sh org-name new-org-name
```

## Create Repositories

```sh
#!/usr/bin/env bash

function create_repo {
    repo_name=$1
    curl -L \
        -u "<username>:<generated access token>" \
        -H "Accept: application/vnd.github.v3+json" \
        -X POST https://github.company.com/api/v3/orgs/RnD/repos \
        -d "{\"name\":\"${repo_name}\",\"private\":true,\"team_id\":3126}"
}

repos=$(cat ./repos)
echo $repos

for repo in $repos; do create_repo ${repo}; done
```

## 在组织间 Clone Repositories

```sh
#!/usr/bin/env bash

function clone_repo {
    repo_name=$1

    git clone --bare git@github.company.com:Engineering/${repo_name}.git \
        && cd ${repo_name}.git \
        && git push --mirror git@github.company.com:RnD/${repo_name}.git
}

repos=$(cat ./repos)
echo $repos

for repo in $repos; do clone_repo ${repo}; done
```
