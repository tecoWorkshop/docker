# Nginx + PHP-fpm

## ビルド

```
$ docker build -t sandbox-phpfpm ./phpfpm
$ docker build -t sandbox-nginx ./nginx
```

## 起動

```
$ docker run --name php -d sandbox-phpfpm
$ docker run --name nginx -p 80:80 --link php:php -d sandbox-nginx
```
