---
layout: post
title:  "微信点餐-点单"
date:   2017-09-20 10:23:39 +0800
categories: java springboot wechat
---

##买家商品点单页面

### Dao层设计与开发
- 实体类创建，与类目表一致，字段与数据库对应。
- 创建对应的映射类。这里我们会根据其中状态进行查询。

```java
/** 根据状态查询. */
List<ProductInfo> findByProductStatus(Integer productStatus);
```

### Service层设计与开发

- 关于业务层，这里就要考虑卖家和买家的业务了，其中买家可以看到上架的商品，订单库存增删，买家后台管理中分页查询所有数据，商品信息添加。
- 状态查询我们用‘0’代表上架和‘1’代表下架。这里采用枚举的方式：

```java
@Getter
public enum ProductInfoEnum {
    UP(0,"上架"),
    DOWN(1,"下架")
    ;

    private Integer code;

    private String name;

    ProductInfoEnum(Integer code, String name) {
        this.code = code;
        this.name = name;
    }
}
```

- 其中关于分页查询，采用Page<>类。其中传值为Pageable,具体通过PageRequest(第几页,每页显示数);

```java
public Page<ProductInfo> findAll(Pageable pageable) {
        return repository.findAll(pageable);
}
    
@Test
public void findAll() throws Exception {
    PageRequest request = new PageRequest(0,2);
    Page<ProductInfo> result = productInfoService.findAll(request);
    log.info("total={}",result.getTotalElements());
    Assert.assertNotEquals(0,result.getTotalElements());
}
```

### Controller层设计与开发

- 买家商品Api，Api大家肯定都知道封装数据。这里我们采用封装成Jason格式。

```jason
{ 
    code: 0 ,
    msg :"success",
    data:[
        {
            name:"热销榜",
            type:0,
            foods:[
                {
                    id:"123456",
                    name:"手抓饼",
                    price:8,
                    description:"手抓饼原名葱抓饼，抓起就吃。",
                    icon:"http://xxxxx.jpg"
                },
                {
                    id:"123457",
                    name:"酸梅汁",
                    price:5,
                    description:"开胃饮料",
                    icon:"http://xxxxx.jpg"
                }
            ]
        }，
        {
            name:"男生最爱",
            type:1,
            foods:[
                {
                    id:"123458",
                    name:"小黄鱼",
                    price:10,
                    description:"精炸小黄鱼，香脆爽口",
                    icon:"http://xxxxx.jpg"
                }
            ]
        }
    ]
}
```

- 这里只是示例，具体按公司要求来，封装数据这块是前后台交互，前端与后端人员需先意见一致。
- 买家端商品页面展示，这里逻辑实现：

```java
@RequestMapping(value="/list")
public ResultVo list(){

    //查询上架商品(数据库查询采用一次性查询)
    List<ProductInfo> productInfoList = productInfoService.findUpAll();
    //查询类目
    //传统方法

//        List<Integer> categoryList = new ArrayList<>();
//        for(ProductInfo productInfo:productInfoList){
//            categoryList.add(productInfo.getCategoryType());
//        }
    //java8 lambda
    List<Integer> categoryList = productInfoList.stream().
    map(e -> e.getCategoryType()).collect(Collectors.toList());
    List<ProductCategory> ProductCategoryList = 
    productCategoryService.findByCategoryTypeIn(categoryList);
    //数据拼装
    List<ProductVO> productVOoList = new ArrayList<>();
    //类目 封装
    for(ProductCategory productCategory : ProductCategoryList){
        ProductVO productVo = new ProductVO();
        productVo.setCategoryType(productCategory.getCategoryType());
        productVo.setCategryName(productCategory.getCategoryName());
        //商品信息封装
        List<ProductInfoVO> productInfoVOList = new ArrayList<>();
        for(ProductInfo productInfo : productInfoList){
            if(productVo.getCategoryType().equals(productInfo.getCategoryType())){
                ProductInfoVO productInfoVO = new ProductInfoVO();
                BeanUtils.copyProperties(productInfo,productInfoVO);
                productInfoVOList.add(productInfoVO);
            }
        }
        productVo.setProductInfoVOList(productInfoVOList);
        productVOoList.add(productVo);
    }

    return ResultVOUtil.success(productVOoList);
}
```

- 提示：
1. 数据封装只封装Api中需要的值，所以这里我们需要建立新的实体类。
2. 数据查询，我们遵循一次查询内容，后做循环处理结果，保证程序高效性。
3. java8特性中的lambda公式。深入钻研java8新特性[点击这里](http://www.importnew.com/11908.html)。
4. chrome中添加JSONview插件获取与安装[点击这里](http://blog.csdn.net/yy228313/article/details/50535246)。

- 前端交互，访问虚拟机的ip地址，此时会弹出请微信端访问，这里我们通过添加修改缓存方式访问页面过滤。

```js
/** url:虚拟机IP/#/order */
/** F12开发者模式，在console中输入*/
document.cookie='openid=abc123';
/** 此时在输入虚拟机地址直接可以访问 */
```

- 接下来就是虚拟机了（前后端数据对接），原视频UP主虽然所有的虚拟机设置都弄好了，但对于我这个虚拟机小白来说，基本的命令都不知道。这里各种坑，自己一点点给填上了，关于centos中dos基础入门，[点击这里](http://jingyan.baidu.com/article/495ba8410ff14d38b30ede01.html)。

```cmd
vim /usr/local/nginx/conf/nginx.conf
/** 按I右下角会出现Insert，此时开启编辑状态 */
/** 更改proy_pass 127.0.0.1为项目运行的IP地址(本机IP) */
/** 反向代理nginx后期讲解 */
/** 按esc退出编辑状态 */
/** 输入:wq 保存并退出*/
nginx -s reload
/** 重启nginx */
```

- 网页访问虚拟机地址，会返现数据对接上,这里又是一个坑,504错误,找来找去，最后是防火强未关，请求不到本机。
- 最后一个就是关于微信官方采用域名对接,虚拟机中修改nginx中设置域名。

```cmd
vim /usr/local/nginx/conf/nginx.conf
/** 按I右下角会出现Insert，此时开启编辑状态 */
/** 更改server_name 127.0.0.1为sell.com */
/** 按esc退出编辑状态 */
/** 输入:wq 保存并退出*/
nginx -s reload
/** 重启nginx */
```

- 本机设置输入指定sell.com会跳转虚拟机的ip地址。访问路径C:\WINDOWS\system32\drivers\etc 管理员权限在最后一行添加 虚拟机IP sell.com 保存。详细教程[点击这里](http://blog.csdn.net/u010234516/article/details/52963954)。

----------
- 原视频UP主慕课网（SpringBoot企业级微信点餐项目）
- 本博客撰写人: XiaoJinZi 转载请注明出处
- 学生能力有限 附上邮箱: 986209501@qq.com  不足以及误处请大佬指责



