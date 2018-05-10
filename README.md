Forked from [kubernetes contrib]
根目录为正式环境使用，每次更新代码时sudo docker-compose up -d后代码更新完自动停止git容器
test目录为测试环境使用，代码实现定时拉取git仓库代码
# Usage
## without docker compose
```bash
docker volume create -d local --name website_sources

docker run --name git-sync -d  \
    -e GIT_SYNC_REPO=https://github.com/openweb-nl/website-static.git \
    -e GIT_SYNC_DEST=/git \
    -e GIT_SYNC_BRANCH=master \
    -e GIT_SYNC_REV=FETCH_HEAD \
    -e GIT_SYNC_WAIT=10 \
    -v website_sources:/git openweb/git-sync:0.0.1

docker run --name nginx \
    -d -p 8080:80 -v website_sources:/usr/share/nginx/html nginx
```
## with docker compose

Create a file called docker-compose.yml with the following content.
```yml
version: '2'

services:
  nginx:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - website_sources:/usr/share/nginx/html:z
    depends_on:
      - git-sync
    restart: always
  git-sync:
    image: openweb/git-sync:0.0.1
    environment:
      GIT_SYNC_REPO: "https://github.com/openweb-nl/website-static.git"
      GIT_SYNC_DEST: "/git"
      GIT_SYNC_BRANCH: "master"
      GIT_SYNC_REV: "FETCH_HEAD"
      GIT_SYNC_WAIT: "10"
    volumes:
      - website_sources:/git:z
    restart: always
volumes:
  website_sources:
    driver: local
```
Then start the containers with running the following command
```bash
docker-compose up -d
```

[kubernetes contrib]: <https://github.com/joemccann/dillinger>