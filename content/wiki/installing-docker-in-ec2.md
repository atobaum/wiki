---
title: "EC2 우분투에 도커 설치하기"
summary: ''
layout: wiki
parent: 
tags: ["Guide", "Docker", "EC2"]
toc: true
latex: false
date: 2020-07-20T13:56:00+09:00
lastmod: 2020-07-20T13:56:00+09:00
---

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce
sudo usermod -aG docker ${USER}
```

```
sudo curl -L https://github.com/docker/compose/releases/download/1.25.0\
-rc2/docker-compose-`uname -s`-`uname -m` -o \
/usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
