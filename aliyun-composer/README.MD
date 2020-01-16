拉取依赖
```
docker run --rm \
    -v ${WORKSPACE}/html:/app \
    -v /docker/composer/cache:/tmp \
    kevelena/aliyun-composer:latest \
    install --no-dev -vv --ignore-platform-reqs --no-scripts
```
