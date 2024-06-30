---
layout: post
title:  "Download files from remote server"
date:   2024-06-30 10:05:00 +0700
categories: jekyll update
---
## Use ssh key
```shell
scp -i ~/path/to/your-key username@public-ip-address:~/path/to/your-project/log/production.log-20240630.gz ~/Downloads
```

## Use password
```shell
scp username@public-ip-address:~/path/to/your-project/log/production.log-20240630.gz ~/Downloads
```
- Enter your server password

## Efficient log check command
```shell
grep -B 30 -A 30 --color "keywords you want to search" production.log
```
