
## 启动
```
docker run -d -v $PWD:/var/www/html --name my-php -p 39002:80 php71-apache
```
## 定时任务
```
FROM php71-apache
RUN echo "* * * * * root php /var/www/html/artisan schedule:run >> /dev/null 2>&1" >> /etc/crontab && cron start
```

php-7.1.30-SHA-256 : a604edf85d5dfc28e6ff3016dad3954c50b93db69afc42295178b4fdf42e026c
![php-7.1.30-SHA-256.png](https://raw.githubusercontent.com/kevelena/docker-hub/master/static/php-7.1.30-SHA-256.png)
