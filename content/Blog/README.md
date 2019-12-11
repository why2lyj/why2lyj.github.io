
├── build.sh  ## 如果 why2lyj.github.io 有更新，运行此脚本可以直接上传到github上
├── hugo      ## hugo_extended_0.58.3_Linux-64bit
├── JssscSpider ## 一个项目主要为爬虫
├── mysql.sql   ## 对应爬虫的SQL语句，查询用
├── why2lyj.github.io ## 自己的github代码

## build.sh

代码

```
# run hugo
docker exec -it f494a4a664c9 bash -c "cd /root/why2lyj.github.io/ && /root/hugo"
# rm
cd /root/build/hugo/why2lyj.github.io && rm -rf 404.html about blog css diary fonts footer.svg images index.html js loaders loader.svg sidebar.svg tags
# mv publich to why2lyj.github.io
mv ./public/* ./
rm -rf ./public/
# git add
git add --all
# git commit
CURRENT_TIME=`date "+%Y-%m-%d %H:%M:%S"`
git commit -m "AUTO COMMIT ${CURRENT_TIME}"
```

f494a4a664c9 是一个Docker的CONTAINER ID, 这个镜像的 Dockerfile 如下：
```
FROM python:3.7-slim-stretch
RUN apt-get update && \
    apt-get install -y gcc && \
    apt-get install -y g++
```

运行命令 ```docker run -it -d -v /root/build/hugo:/root/ --name hugo gcc:1.0.0```

自己已经装过git了，做了免密


## why2lyj.github.io

farseer.vip 的源代码，托管在 https://github.com/why2lyj/why2lyj.github.io

使用的是 hugo-refresh https://github.com/PippoRJ/hugo-refresh

我改动过几个地方，未来要更新的时候务必小心对待。


