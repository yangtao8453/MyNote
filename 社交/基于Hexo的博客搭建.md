## 本地环境

### Node.js

* 让JavaScript也可以成为服务端语言，官网：https://nodejs.org/en/

### Git

* 版本控制、管理工具，官网：https://git-scm.com/

### Hexo

* npm全局安装

  ```powershell
  npm install hexo-cli -g
  ```



## 远程仓库配置

*这里使用GitHub，Gitee也大同小异，只是在pages需要实名认证以及自定义域名时需要收费*

### 仓库名

`仓库名一定要是 {用户名}.github.io，仓库权限为public`

![image-20220108000615356](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108000615356.png) 

### SSH绑定

*使用SSH关联本地环境与远程仓库*

1. 创建密钥对

   ```bash
   ssh-keygen -t rsa -C "{GitHub注册邮箱}"
   ```

2. 一路确认，在用户目录下的.ssh目录下有两个文件，其中id_rsa.pub为公钥，需要复制里面全部的内容

3. 回到==GitHub==，进入==settings==，目录找到==SSH and GPG keys==，==new SSH==，将复制的公钥内容复制到==Key==框中，==Add SSH Key==即可

4. 回到本地验证，看到成功绑定即可

   ```bash
   $ ssh -T git@github.com
   Hi yangtao8453! You've successfully authenticated, but GitHub does not provide shell access.
   ```



## 本地博客

*创建一个本地文件夹作为博客的根目录，用于存放需要推送到github仓库的博客静态文件*

1. 初始化（将hexo项目克隆到本地）

   ```bash
   hexo init
   ```

2. 启动本地服务

   ```bash
   hexo s
   ```

3. 访问http://localhost:4000，可以看到博客页面，默认有一篇《Hello World》

   ![image-20220108002630494](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108002630494.png)



## 部署远程

1. 来到博客根目录，编辑文件 `_config.yml`

   ```yaml
   deploy: 
     type: git
     repository: {仓库的地址}  		 # 推荐使用SSH的地址
     branch: main
   ```

2. 安装hexo部署工具

   ```bash
   npm install hexo-deployer-git --save
   ```

3. `博客根目录 \ source \ _posts`下新建markdown文件，这个就是博客正文内容

4. 编辑完正文内容后，回到根目录，清理缓存、生成html、再部署即可

   ```
   hexo clean
   hexo g
   hexo d
   ```

5. 等待提交完成后，过一会就可以通过 https://xxxxx.github.io/ (xxxxx为github用户名)看到博客首页及内容了



## 主题推荐

### fluid

* github地址：https://github.com/fluid-dev/hexo-theme-fluid

* 使用教程：https://hexo.fluid-dev.com/docs/start/

* 个人效果图：

  ![image-20220108004341491](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108004341491.png)



## 集成评论

1. 访问https://github.com/apps/utterances，授权

   ![image-20220108005943107](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108005943107.png) 

2. 前往https://utteranc.es/进行配置

   ![image-20220108010642615](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108010642615.png) 

3. 选择主题，网页中添加如下js代码即可集成评论（github登录），一般hexo主题都可以在 `_config.yml`中配置是否启用评论，集成的插件类型，无需手动添加js代码

   ![image-20220108010824374](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108010824374.png) 



## 绑定个人域名

1. 配置域名指向存放博客静态文件的github仓库

   ![image-20220108011253644](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108011253644.png)

2. GitHub设置

   ![image-20220108011427571](https://gitee.com/yangtao8453/picgo/raw/master/img/image-20220108011427571.png)

3. 博客根目录 \ source 目录下添加一个 CNAME 文件，内容为个人域名，重新部署；或者仓库直接添加一个 CNAME文件，防止每次重新部署后都要手动在github的pages重新保存域名。

