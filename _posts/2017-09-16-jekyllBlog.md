---
layout: post
title:  "Jekyll结合GitHub搭建简易博客"
date:   2017-09-16 10:15:20 +0800
categories: jekyll update
---


## Windows下Jekyll环境准备
- ### 1. Jekyll 环境配置安装
  ***
  - #### 1.1 Ruby安装
  [Ruby.exe 2.3.6 提取码：hjpj](http://pan.baidu.com/s/1kUNQneZ)
 
  **直接双击安装完程序**

  **控制台输入**
  ``` cmd
  ruby -v
  ```
  **安装成功如图**
  ![RubyPIC]({{site.url}/downloads/jekyllBlog/rubySuccess.png})
  ***
  - #### 1.2 RubyGem安装
  [RubyGem.zip 提取码：8uox](http://pan.baidu.com/s/1eS6F8Hk)
  
  **解压文件**
  
  **控制台跳转文件目录下**
  ``` cmd
  cd 压缩文件路径
  ruby setup.rb
  ```
  ![RubyGemSetUp]({{site.url}/downloads/jekyllBlog/RubyGem1.png)
  
  **版本查看**
  ``` cmd
  gem -v
  ```
  ![RubyGemVer]({{site.url}/downloads/jekyllBlog/RubyGem2.png)
  
  **Jekyll安装**
  ``` cmd
  gem install jekyll
  ```
  ![RubyGemJekyll]({{site.url}/downloads/jekyllBlog/RubyGem3.png)
  ***
- ### 2. 搭建个人博客站点
  > [本文编写参考Jekyll官方文档](http://jekyll.com.cn/docs/home/)
  
  - 2.1 搭建个人博客
  > 这里和创建项目一样，选择自己项目路径，本人创建在E盘中，新建一个项目工作空间。具体cmd操作如下
  ```cmd
  E:
  cd E:\Blog
  jekyll new XiaoJinZi
  ```
  > (第一个坑)Dependency Error: Yikes! It looks like you don't have bundler or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- bundler' If you run into trouble, you can find helpful resources at http://jekyllrb.com/help/!

  ```cmd
  gem install bundler
  ```
  > 当我认为一切都已经解决，结果第二个坑来了
  
  > (网络问题)Retrying dependency api due to error (2/4): Bundler::HTTPError Network error while fetching https://index.rubygems.org/api/v1/dependencies?gems=jekyll%2Cjekyll-feed%2Cminima%2Ctzinfo-data (execution expired).....
  
  > 这里修改官方数据源
  
  ```cmd
  gem sources --remove https://rubygems.org/
  gem sources -a https://gems.ruby-china.org/
  ```
  > 这里又出现一个证书问题
  
  > ERROR:  SSL verification error at depth 1: unable to get local issuer certificate (20)
ERROR:  You must add /O=Digital Signature Trust Co./CN=DST Root CA X3 to your local trusted store
Error fetching https://gems.ruby-china.org/:
        SSL_connect returned=1 errno=0 state=error: certificate verify failed (https://gems.ruby-china.org/specs.4.8.gz)
    
  ```cmd
  gem sources -a http://gems.ruby-china.org/ 
  gem sources -l
  ```
  > 安装rails框架
  
  ```cmd
  gem install rails
  ```
  
  > 激动人心的时刻到了
  
  ```cmd
  E:
  cd E:\Blog
  jekyll new XiaoJinZi
  jekyll server
  ```
  ![FirstBlog]({{site.url}/downloads/jekyllBlog/BlogPicture.png)
  
  ![Success]({{site.url}/downloads/jekyllBlog/BlogSuccess.png)
  
  > 接下来就是将自己的markDown文件放入_post文件夹中，文件命名(2017-09-15-springboot-2)日期+项目名中间用-连接。同时文件内容开头需要设置如下
  
  ```yml
    ---
    layout: post
    title:  "项目标题"
    date:   2017-09-15 14:18:39 +0800
    categories: jekyll update
  ---
  ```
  
  测试 
  
  ```cmd
  E:
  cd E:\Blog\XiaoJinZi
  jekyll server
  ```
  这里登陆127.0.0.1：4000就能看到效果
  
    ![BrowseOpen]({{site.url}/downloads/jekyllBlog/BrowsSuccess.png)
  
  - 2.2进阶教程
    通过github部署并发布项目，通过创建一个新的仓库，将文件上传。[github操作学习](http://www.imooc.com/learn/390)
    
    > 首先需要将_site中的assets放到根目录下。首页设置，在_config.yml文件中，其中BaseUrl: /github仓库名(博客访问首页) url: 域名//github仓库名(用于图片等路径获取) 添加菜单功能，例如about.md文件设置。

    > 关于图片引用，根目录下创建一个新的文件夹，里面放上图片。需要通过使用{{site.url}}/图片文件夹/图片名。
    
    ![photoUrl]({{site.url}/downloads/jekyllBlog/photoUrl.png)

    
  