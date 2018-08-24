---
layout:     post                    # 使用的布局（不需要改）
title:      vue better-scroll            # 标题 
subtitle:   better-scroll无法滚动bug    #副标题
date:       2018-08-24              # 时间
author:     Jeremy                 # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Vue.js 
    - JavaScript
---

## 问题
> 使用better-scroll出现无法拉动页面的情况

之前的一个移动端项目使用better-scroll插件时会偶尔出现页面无法拉动的情况（只能滑动到红线处）
往上拉可以看到下面的内容，但是松手后就会回到红线处
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fukvnarm05j30ku0xmq4t.jpg)

## 解决
> 发现问题

使用better-scroll初始化时要在数据加载完成后，因为它在初始化的时候，
会计算父元素和子元素的高度和宽度，来决定是否可以纵向和横向滚动。从控
制台打印better-scroll对象会发现他有两个属性 `scrollerHeight`和 `wrapperHeight`
当 `scrollerHeight `>= `wrapperHeight` 时才能正常拉动，出现上述bug的原因时因为
图片没加载完成的情况下better-scroll已经初始化，这时会造成`scrollerHeight ` < `wrapperHeight`
所以页面无法拉动

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fukw7xis35j30ga10utib.jpg)

> 解决方案--图片预加载

通过预加载图片使其占好高度，防止高度塌陷，通过watch监听

      <template>
       <div v-show=show>
           <img src="xxxxx" alt="">
           <img src="xxxxx" alt="">
           <img src="xxxxx" alt="">
           <img src="xxxxx" alt="">
       </div>
    </template>
    <script>
        export default {
            mounted () {
                var _this = this
                let imgs = document.querySelectorAll('img')
                console.log(imgs)
                Array.from(imgs).forEach((item)=>{
                    let img = new Image()
                    img.onload = ()=>{
                        this.count++
                    }
                    img.src=item.getAttribute('src')
                })
            },
            data () {
                return {
                    count : 0,
                    show : false
                }
            },
            watch : {
                count (val,oldval) {
                    if(val == 4){
                        this.show = true
                        alert("加载完毕")
                        //然后可以对后台发送一些ajax操作
                    }
                }
            }
        }
    </script>
