+++
title = "Docker-Redmine"
date = 2025-03-16T02:56:41+09:00
weight = 5
draft = false
categories = ["Server"]
tags = ["Author:だるま", "Level:中級", "Type:Knowledge", "Docker"]
+++

## docker上にRedmineを構築する

### 注意事項

※VM上のUbuntuもしくはDebianを使用する前提で記載しています
※これが一番楽でしたｗ

### dockerインストール公式サイト参照
https://docs.docker.com/engine/install/

### docker-compose.ymlの作成場所

```cmd
# mkdir /usr/local/redmine
# cd /usr/local/redmine
# vi docker-compose.yml
```

### docker-compose.ymlの編集

```yml
services:
  redmine:
    image: redmine:latest
    container_name: redmine
    restart: always
    ports:
      - 80:3000
    environment:
      TZ: Asia/Tokyo
      REDMINE_DB_MYSQL: mysql
      REDMINE_DB_DATABASE: redmine
      REDMINE_DB_USERNAME: redmine
      REDMINE_DB_PASSWORD: redmine
      REDMINE_DB_ENCODING: utf8mb4
    depends_on:
      - mysql
    volumes:
      - web:/usr/src/redmine/files
  mysql:
    image: mysql:latest
    container_name: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: P@ssw0rd
      MYSQL_DATABASE: redmine
      MYSQL_USER: redmine
      MYSQL_PASSWORD: redmine
      TZ: 'Asia/Tokyo'
    command: mysqld --character-set-server=utf8 --collation-server=utf8_unicode_ci
    volumes:
      - db:/var/lib/mysql
volumes:
  web:
  db:
```

  ## docker-compose実行
 ```cmd
 docker compose up -d
 ```
