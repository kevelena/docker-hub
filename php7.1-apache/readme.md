
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

## Jenkins 自动化构建脚本 (Laravel 项目)

代码来源设置

![source.png](https://raw.githubusercontent.com/kevelena/docker-hub/master/static/source.png)

```

#!/bin/bash
#对外端口
port="20005"
#删除vendor
echo "正在删除目录 ${WORKSPACE}/html/vendor"
rm -rf ${WORKSPACE}/html/vendor
#修改配置文件
mv ${WORKSPACE}/html/.env-test ${WORKSPACE}/html/.env
echo "开始下载安装PHP依赖"
docker run --rm \
    -v ${WORKSPACE}/html:/app \ #项目映射
    -v /docker/composer/cache:/tmp \ #缓存映射
    composer \
    install --no-dev -vv --ignore-platform-reqs --no-scripts
#判断容器是否运行中,如运行中则删除容器
docker ps -a | grep ${JOB_NAME}
if [[ $? -eq 0 ]]; then
	docker stop ${JOB_NAME}
	docker rm ${JOB_NAME}
fi
#启动容器
docker run -d \
  -v /wwwroot/${JOB_NAME}:/var/www/html/storage \ #持久化存储storage
  --name ${JOB_NAME} \ #以${JOB_NAME}命名容器
  -p ${port}:80 \ #端口映射
  php71-apache
#把项目复制到容器内
docker cp ${WORKSPACE}/html ${JOB_NAME}:/var/www/
#添加权限
docker exec -i ${JOB_NAME} chmod -R 777 /var/www/html/storage/
docker exec -i ${JOB_NAME} chmod -R 777 /var/www/html/bootstrap/
#执行命令
docker exec ${JOB_NAME} php /var/www/html/artisan schedule:run >> /dev/null 2>&1
#判断是否构建成功
docker ps | grep ${JOB_NAME}
if [[ $? -ne 0 ]]; then
	echo "构建失败"
	docker stop ${JOB_NAME}
	docker rm ${JOB_NAME}
else
	echo "构建成功"
fi

```
