---
title: Chef 学习笔记
date: 2018-07-01 12:32:07
modify_date: 2020-10-12
tags: Chef
key: Chef-Learning-Note-2018-07-01
---

## 登录 Chef Server 下载 Starter Kit

1. 打开 [Chef hosted 官网](https://manage.chef.io/signup) 注册并登录
2. 在 chef server 中创建新的组织
3. 选择组织并下载并解压 Starter Kit

<!--more-->

## 检查是否可以正确连接到 Chef Server

```bash
$ cd ~/chef-repo
$ ls ~/chef-repo/.chef
username.pem
knife.rb

# 测试连接
$ knife ssl check
Connecting to host api.chef.io:443
Successfully verified certificates from 'api.chef.io'

# 生成 cookbook 框架
$ mkdir ~/chef-repo/cookbooks
$ chef generate cookbook ~/chef-repo/cookbooks/your_cookbook_name
```

## 本地测试 Recipes

```bash
chef-client --local-mode hello.rb
```

## 上传 cookbook 到 Chef server

```bash
$ cd ~/chef-repo/
$ knife cookbook upload learn_chef_httpd
Uploading learn_chef_httpd [0.1.0]
Uploaded 1 cookbook.

# 查看 cookbook 列表
$ knife cookbook list
learn_chef_httpd   0.1.0
```

## 启动 Node 关联到 Chef Server 并执行 Recipes

```bash
$ cd ~/chef-repo
$ knife bootstrap 192.168.88.11 --ssh-user vagrant --sudo --identify-file ~/.ssh/id_rsa --node-name node-centos --run-list 'recipe[learn_chef_httpd]'

$ knife node list
node-centos

$ knife node show node-centos
Node Name:   node-centos
Environment: _default
FQDN:        localhost
IP:          10.0.2.15
Run List:    recipe[learn_chef_httpd]
Roles:
Recipes:     learn_chef_httpd, learn_chef_httpd::default
Platform:    centos 7.2.1511
Tags:
```

Update your node's configuration

- You ran `knife bootstrap` to associate your node with the Chef server and do an initial check-in. Bootstrapping is a one-time process.
- The `knife ssh` command enables you to update your node's configuration when your cookbook changes.

## Chef 模板

- Chef server created what's called a `node object`
- The `<%= %>` syntax enables you to provide placeholders in your template file.

## 更新 cookbook 的版本等信息

```bash
vim ~/chef-repo/cookbooks/learn_chef_httpd/metadata.rb
```

```ruby
name 'learn_chef_httpd'
maintainer 'The Authors'
maintainer_email 'you@example.com'
license 'all_rights'
description 'Installs/Configures learn_chef_httpd'
long_description 'Installs/Configures learn_chef_httpd'
version '0.2.0'
issues_url 'https://github.com/learn-chef/learn_chef_httpd/issues' if respond_to?(:issues_url)
source_url 'https://github.com/learn-chef/learn_chef_httpd' if respond_to?(:source_url)
```

## 在 Node 上执更新后的 cookbook

```bash
knife ssh 'name:node-centos' 'sudo chef-client' -x vagrant -i ~/.ssh/id_rsa -a ipaddress
```

## 从 Berkshelf 下载 cookbook

Berkshelf is a tool that helps you resolve cookbook dependencies.

```bash
$ cd ~/chef-repo
$ vim Berksfile
source 'https://supermarket.chef.io'
cookbook 'chef-client'

$ berks install

$ ls -1 ~/.berkshelf/cookbooks
chef-client-10.1.0
cron-6.1.1
logrotate-2.2.0
windows-4.3.3

$ berks upload
Uploaded windows (4.3.3) to: 'https://api.chef.io/organizations/learning2018'
Uploaded logrotate (2.2.0) to: 'https://api.chef.io/organizations/learning2018'
Uploaded cron (6.1.1) to: 'https://api.chef.io/organizations/learning2018'
Uploaded chef-client (10.1.0) to: 'https://api.chef.io/organizations/learning2018'
```

## 创建 role

Roles enable you to focus on the function your node performs collectively rather than each of its individual components (its run-list, node attributes, and so on). For example, you might have a web server role, a database role, or a load balancer role. Here, you'll create a role named web to define your node's function as a web server.

```bash
mkdir ~/chef-repo/roles
vim ~/chef-repo-roles/web.json
```

```json
{
  "name": "web",
  "description": "Web server role.",
  "json_class": "Chef::Role",
  "default_attributes": {
    "chef_client": {
      "interval": 300,
      "splay": 60
    }
  },
  "override_attributes": {},
  "chef_type": "role",
  "run_list": [
    "recipe[chef-client::default]",
    "recipe[chef-client::delete_validation]",
    "recipe[learn_chef_httpd::default]"
  ],
  "env_run_lists": {}
}
```

```bash
knife role from file

knife role list

knife role show web

knife node run_list set node-centos 'role[web]'

knife node show node1-centos --run-list
```

## 启动 chef-client 执行 role

```bash
knife ssh 'role:web' 'sudo chef-client' -x vagrant -a ipaddress
```

## Clean up your environment

### Delete the node from the Chef server

```bash
knife node delete node-centos --yes
knife client delete node-centos --yes
```

### Delete your cookbook from the Chef server

```bash
knife cookbook delete learn_chef_httpd --all --yes
```

### Delete the role from the Chef server

```bash
knife role delete web --yes
```

### Delete the RSA private key from your node

```bash
sudo rm /etc/chef/client.pem
```

## Knife Subcommands Summary

### knife node

- `list`

```bash
knife node list
```

- `show`

```bash
# To show basic information about a node, truncated and nicely formatted
knife node show NODE_NAME

# To show all information about a node, nicely formatted
# -l, --long
knife node show -l $host

# To list a single node attribute
# -a ATTR, --attribute ATTR
knife node show $host -a ATTRIBUTE_NAME

# To view the FQDN for a node named
knife node show $host -a fqdn

knife node show $host -a cookbooks

# To view the run-list for a node named
# -r, --run-list
knife node show $host -r
```

- `run_list add`

  - A run-list is an ordered list of **roles** and/or **recipes** that are run in the exact order defined in the run-list; if a recipe appears more than once in the run-list, Chef Infra Client will not run it twice.

  - Both **roles** and **recipes** must be in quotes.

  - Use the `run_list add` argument to add **run-list items (roles or recipes)** to a node.

```bash
# Syntax
knife node run_list add NODE_NAME RUN_LIST_ITEM

# To add a role to a run-list
knife node run_list add $host 'role[NAME]'

# To add a recipe to a run-list using the fully qualified format
knife node run_list add $host 'recipe[COOKBOOK::RECIPE]'

# To add a recipe to a run-list using the cookbook format
knife node run_list add $host 'COOKBOOK::RECIPE_NAME'

# To add the default recipe of a cookbook to a run-list
knife node run_list add $host 'COOKBOOK'

# To add roles and recipes to a run-list
knife node run_list add $host 'role[NAME], recipe[COOKBOOK::RECIPE]'
```

- `run_list remove`

Use the `run_list remove` argument to remove **run-list items (roles or recipes)** from a node. 

```bash
# Syntax
knife node run_list remove NODE_NAME RUN_LIST_ITEM

# To remove a role from a run-list
knife node run_list remove $host 'role[ROLE_NAME]'

# To remove a recipe from a run-list using the fully qualified format
knife node run_list remove $host 'recipe[COOKBOOK::RECIPE_NAME]'
```

- `run_list set`

Use the `run_list set` argument to set the run-list for a node.

```bash
# Syntax
knife node run_list set NODE_NAME RUN_LIST_ITEM
```

### knife status

```bash
knife status

knife status | grep -P -i 'pattern'
```

### knife runs

```bash
# List running status for all hosts
knife runs list

# List running status for a specific host
knife runs list $host

# Get the details for a run_id
knife runs show $run_id
```

### knife role

```bash
knife role list

knife role show ROLE_NAME
```
