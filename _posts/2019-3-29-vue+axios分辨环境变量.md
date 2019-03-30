---
layout:     post                    # 使用的布局（不需要改）
title:      vue-cli 3.0                 # 标题 
subtitle:   vue-cli3.0 打包区分环境      #副标题
date:       2019-03-29         # 时间
author:     Jeremy                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Vue.js 
    - JavaScript
---
                    
## 使用vue-cli 3.0 打包区分环境配置

> 问题描述

  在vue-cli3的项目中，
  
  npm run serve时会把process.env.NODE_ENV设置为‘development’；
  
  npm run build 时会把process.env.NODE_ENV设置为‘production’；
  
  此时只要根据process.env.NODE_ENV设置不同请求url就可以很简单的区分出本地和线上环境。
  
  实际开发打包时线上环境可能分多种，比如测试环境和生产环境等等。
  在vue-cli2中打包时可以修改 “build” 和 “config”中的文件来区分不同的线上环境
  
  而vue-cli3的0配置，无法直接修改打包文件，那么怎么区分不同的线上环境呢？ 也就是说npm run build时怎么手动更改process.env.NODE_ENV？
  
> 解决方案
  
  具体操作步骤如下
  
  1. package.json添加  "test": "vue-cli-service build --mode text"  //test为测试环境变量
  
    {
        ···
        "scripts": {
          "serve": "vue-cli-service serve",    //本地运行，会把process.env.NODE_ENV设置为‘development’
          "test": "vue-cli-service build --mode test",  
          // 打包，会把process.env.NODE_ENV设置为步骤2中‘.env.test’文件设置的值。
          // 注意，这里 “--mode 名字“ 要和步骤2中文件名“.env.名字”名字保持一致
           "build": "vue-cli-service build"    //打包， 会把process.env.NODE_ENV设置为‘production’
        },
        "dependencies": {
           ···    
        },
        ···
     }
     
  2  在项目根目录添加文件“.env.test”，其内容：
  
    NODE_ENV = 'test'
    
  3  添加区分环境的js文件 setBaseUrl.js
  
    let baseUrl;   
    switch (process.env.NODE_ENV) {
        case 'development':
            baseUrl = "http://localhost:xxxx/"  //这里是本地的请求url
            break
        case 'test':   // 注意这里的名字要和步骤二中设置的环境名字对应起来
            baseUrl = "https://www.jeremysang.cn/"  //这里是测试环境中的url
            break
        case 'production':
            baseUrl = "https://www.jeremysang.cn"   //生产环境url
            break
    }
    
    export default baseUrl; 
    
  4  在封装好的axios文件中引入
  
    import axios from 'axios'
    import baseUrl from './setBaseUrl'
    
    axios.defaults.withCredentials = true;
    axios.defaults.baseURL = baseUrl;
    
    ····
    
  5  这样，npm run test即打测试环境包，npm run build则打生产包   
  
  通过改变process.env.NODE_ENV值区分打包环境，但是webpack打包时针对process.env.NODE_ENV===‘production’和其他情况打出来的包结构和大小都不一样；
  
  为了消除这种差异，可以使用其他变量比如process.env.VUE_APP_TITLE来区分环境
  
   在项目根目录添加文件“.env.test”和“.env.build”，其内容分别为： 
   
    NODE_ENV = 'production'      //测试环境
    VUE_APP_TITLE = 'test'
    
    NODE_ENV = 'production'      //生成环境
    VUE_APP_TITLE = 'production' 

   区分环境
   
     const = process.env.NODE_ENV === 'development' ? 'development' :
        process.env.VUE_APP_TITLE === 'test' ? 'test' : 'production';
        
        

