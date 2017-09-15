---
layout: post
title:  "微信点餐"
date:   2017-09-15 14:18:39 +0800
categories: jekyll update
---

## 第二章
- **1.项目设计**
  - 1.1 角色划分
  
    ```
    graph LR
    A[买家-手机端]-->|点餐|B[卖家-pc端]
    ```
    
  - 1.2 功能模块划分
   
    - [x] 商品
        - [x] 商品列表 
    - [x] 订单
        - [x] 订单创建
        - [x] 订单查询
        - [x] 订单取消
        - [x] 其他
    - [x] 类目
        - [x] 订单管理
        - [x] 商品管理
        - [x] 类目管理
        - [x] 其他
        
    > ![functionModel](/downloads/functionModel.png)

  - 1.3 部署架构
    
    ```
    graph TB
    A[微信]
    B[浏览器]
    A-->C[Nginx]
    B-->C
    C-->D[tomcat]
    D-->E[缓存服务器]
    D-->F[Mysql]
    ```

- **2.架构与基础框架**
  - 2.1 架构演进
    > ![frameWork]({{site.url}}/XiaoJinZi/downloads/frameWork.png)
  - 2.2 基本框架
    - [x] 阿里系
        - [1] Duboo(服务化治理)
        - [2]Zookeper(服务化注册中心)
        - [3] SpringMvc-SpringBoot
        - [4] ....
    - [x] Spring Cloud 栈
        - [1] Spring Cloud
        - [2] NetFlix Eurelca
        - [3] Spring Boot
        - [4] ....
- **3.数据库设计**
  - 3.1 表与表之间关系
    
    ```
    graph TB
    A[类目表-product_category]
    -->
    B[商品表-product_info]
    B-->C[订单详情表-order_detail]
    C-->D[订单主表-order_master]
    E[卖家信息表seller_info]
    ```
    
  - 3.2 建表Sql
  
    - 3.2.1 商品表
    ```sql
    create table `product_info`(
	`product_id` varchar(32) NOT NULL,
	`product_name` varchar(64) not null comment '商品名称',
	`product_price` decimal(8,2) not null comment '商品价格',
	`product_stock` int not null comment '库存',
	`product_description` varchar(64) comment '描述',
	`product_icon` varchar(512) comment '图片',
	`category_type` int not null comment '类目编号',
	`create_time` timestamp not null default current_timestamp comment '创建时间',
	`update_time` timestamp not null default current_timestamp on update current_timestamp 
		comment '更新时间',
	primary key (`product_id`)
    ) comment '商品表';
    ```
    > 备注：其中更新时间中添加了更新事件，表示在更新的时候自动更新。
    
    - 3.2.2 类目表
    
    ```sql
    create table `product_category`(
	`category_id` int not null auto_increment,
	`category_name` varchar(64) not null comment '类目名称',
	`category_type` int not null comment '类目编号',
	`create_time` timestamp not null default current_timestamp comment '创建时间',
	`update_time` timestamp not null default current_timestamp on update current_timestamp 
		comment '更新时间',
	primary key (`category_id`),
	unique key `uqe_category_type` (`category_type`)
    ) comment '类目表';
    ```
    > 备注：这里添加类目编号约束。
    一个表能定义多个unique 约束，但只能定义一个primary 约束。而且，UNIQUE 约束允许 NULL 值，这一点与 PRIMARY KEY 约束不同。不过，当与参与 UNIQUE 约束的任何值一起使用时，每列只允许一个空值。
    
    - 3.2.3 订单表
    
    ```sql
    create table `order_master`(
	`order_id` varchar(32) not null,
	`buyer_name` varchar(32) not null comment '买家姓名',
	`buyer_phone` varchar(32) not null comment '买家电话',
	`buyer_address` varchar(128) not null comment '买家地址',
	`buyer_openid` varchar(64) not null comment '买家微信opendid',
	`order_amount` decimal(8,2) not null comment '订单总金额',
	`order_status` tinyint(3) not null default '0' comment '订单状态新订单0',
	`pay_staus` tinyint(3) not null default '0' comment '支付状态未支付0',
	`create_time` timestamp not null default current_timestamp comment '创建时间',
	`update_time` timestamp not null default current_timestamp on update current_timestamp 
		comment '更新时间',
	primary key (`order_id`),
	key `idx_buyer_openid` (`buyer_openid`)
    ) comment '订单表';
    ```
    
    - 3.2.4 订单详情表
    ```sql
    create table `order_detail`(
	`detail_id` varchar(32) not null,
	`order_id` varchar(32) not null,
	`product_id` varchar(32) not null,
	`product_name` varchar(64) not null comment '商品名称',
	`product_price` decimal(8,2) not null comment '商品价格',
	`product_icon` varchar(512) not null comment '商品图片',
	`create_time` timestamp not null default current_timestamp comment '创建时间',
	`update_time` timestamp not null default current_timestamp on update current_timestamp 
		comment '更新时间',
	primary key (`detail_id`),
	key `idx_order_id` (`order_id`)
    ) comment '订单详情表';
    ```
    
    
  
  
