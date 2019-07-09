
## 启动
```
docker run -d -v $PWD:/var/www/html --name my-php -p 39002:80 php71-apache
```
## 定时任务
```
FROM php71-apache
RUN echo "* * * * * root php /var/www/html/artisan schedule:run >> /dev/null 2>&1" >> /etc/crontab && cron start
```
