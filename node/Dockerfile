FROM node:10.15.3

# Create app directory
WORKDIR /usr/src/app

RUN chmod -R 777 /usr/local/lib/node_modules/
# install cnpm
RUN npm install -g cnpm --registry=https://registry.npm.taobao.org \
    && cnpm update \
    && cnpm rebuild node-sass \
    && cnpm rebuild vue-template-compiler \
    && yarn add vue-template-compiler --dev --production=false
