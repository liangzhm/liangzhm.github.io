---
title: 如何将vue项目部署到heroku上供外网访问
date: 2019-06-04 15:06:47
tags:
---
## 1. 在heroku网站上注册账号
https://www.heroku.com/
## 2. 安装heroku-cli
https://devcenter.heroku.com/articles/heroku-cli#download-and-install
全局安装heroku命令
npm install -g heroku
查看版本
heroku --version
开始登陆
heroku login
会自动打开浏览器，登陆后，显示done
## 3. 创建app
  进入项目根目录 cd myapp
  创建heroku项目 heroku create 会随机生成一个名称，在网站内可以看到，同时也是以后的访问路径前缀
## 4. 修改配置文件
部署前记得将配置文件中baseUrl改为'./'
将.gitignore文件中的dist一行注释掉，需要将dist目录提交到master分支
## 5. 修改package.json
   在package.json 文件中的scripts中增加以下两句：
   "postinstall": "npm run build",
   "start": "node server.js"
## 6. 在项目根目录下创建server.js文件
```
    const express = require('express');
    const path = require('path');
    const serverStatic = require('serve-static');

    const app = express();
    app.use(serverStatic(path.join(__dirname, 'dist')));
    const port = process.env.PORT || 5000;
    app.listen(port);
    console.log(port);
```
同时记得npm i 上面那些包
# 7. 打包
   npm run build 一下，然后提交到master分支
# 8. 将heroku和github进行关联
   运行 heroku git:remote -a floating-sands-17593（刚才生成的随机项目名字）
# 9. 设置heroku配置
   运行 heroku config:set NPM_CONFIG_PRODUCTION=false
   NPM_CONFIG_PRODUCTION=false的意思是既安装dependencies里的依赖，也安装devDependencies里的依赖
# 10. 设置heroku执行语言
  运行 heroku buildpacks:set heroku/nodejs
  意思是让heroku以nodejs的服务运行
# 11. 部署
    ```
    git add .
    git commit -m"bushu"
    git push heroku master

    ```
可以看到部署的进度和部署状态，后面有部署的网址，打开即可看到网址已经成功的部署到外网可以访问。