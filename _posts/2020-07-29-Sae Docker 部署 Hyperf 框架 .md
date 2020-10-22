---
layout: mypost
title: Sae Docker 部署 Hyperf 框架 
categories: [Hyperf]
---

好多年不是用使用新浪的 SAE 了,今天去后台看还有 500 多云豆，想来就搞点东西放上去吧。
创建应用的时候发现新增了很多语言，而且还支持 Docker 部署。

![image](https://tva2.sinaimg.cn/large/006LCYVcgy1gh6gwwyqylj31z6170th6.jpg)

既然支持 Docker ，那么就部署一个 Swoole 异步框架 Hyperf 上去吧。框架的介绍我这里就不赘述了，可以[点击这里查看框架详情](https://hyperf.wiki/2.0/#/)

这里我选择使用 Dockerfile 部署，输入二级域名跟应用名称，然后创建。

创建成果后点击运行环境>代码管理，根据提示把代码拉取到本地。

![image](https://tvax2.sinaimg.cn/large/006LCYVcgy1gh6h6na83sj315a12877s.jpg)

拉取代码到本地后是一个空文件夹，所有我们要准备 Dockerfile 跟相关代码

```
├── Dockerfile # Dockerfile 文件
└── code # 预先准备好的 hyperf 框架骨架代码
    ├── README.md
    ├── app
    ├── bin
    ├── composer.json
    ├── config
    ├── deploy.test.yml
    ├── installer
    ├── phpstan.neon
    ├── phpunit.xml
    └── test
```
这里我们要需要修改 Hyperf 框架启动的端口为 5050，这是 sae 的容器要求。修改文件在`code/config/autoload/server.php` 修改为如下

```php
//...其他代码
'servers' => [
        [
            'name' => 'http',
            'type' => Server::SERVER_HTTP,
            'host' => '0.0.0.0',
            'port' => 5050,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                SwooleEvent::ON_REQUEST => [Hyperf\HttpServer\Server::class, 'onRequest'],
            ],
        ]
//...其他代码
```

Dockerfile 文件
```dockerfile
FROM hyperf/hyperf:latest
LABEL maintainer="Hyperf Developers <group@hyperf.io>" version="1.0" license="MIT"

# update
RUN set -ex \
    && apk update \
    # install composer
    && cd /tmp \
    && wget https://mirrors.aliyun.com/composer/composer.phar \
    && chmod u+x composer.phar \
    && mv composer.phar /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
    && composer --version \

    # show php version and extensions
    && php -v \
    && php -m \
    #  ---------- some config ----------
    && cd /etc/php7 \
    # - config PHP
    && { \
        echo "upload_max_filesize=100M"; \
        echo "post_max_size=108M"; \
        echo "memory_limit=1024M"; \
        echo "date.timezone=Asia/Shanghai"; \
    } | tee conf.d/99-overrides.ini \
    # - config timezone
    && ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
    && echo "${TIMEZONE}" > /etc/timezone \
    # ---------- clear works ----------
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n" 

COPY code /opt/www
WORKDIR /opt/www
RUN composer install --no-dev -o -vvv

EXPOSE 9501

ENTRYPOINT ["php", "/opt/www/bin/hyperf.php", "start"]
```

一切准备就绪后，git 提交一下。
```shell
# git add .
# git commit -m 'init'
# git push -u origin master
```

`git push` 后容器会自动进行构建。然后就等构建成功了。

打开默认域名，OK 啦
![image](https://tva4.sinaimg.cn/large/006LCYVcgy1gh6hfnvkcuj30t80eamy6.jpg)