# 小程序开发注意

### 1.小程序静态资源的处理

小程序对小程序的代码包体积有一定的要求， 要求不能大于 2048kb (就是不能超过2M)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200510190203901.png#pic_center)
但是项目的一些静态资源占用的空间是很大的，实际项目中图片资源都是通过服务器返回，而非本地资源，所以 2M 的大小可以满足大部分的需求



在wxss中也是不可以引入本地的图片或者字体等资源的，它会报错，因为小程序的wxss不支持本地资源路径，只支持网络路径（(http:// 或 https://）,或者base64路径。

**注意：在 app.json 中配置 tabBar 时所设置的图标只支持本地资源路径。（不能使用网络路径）**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200510190614460.png#pic_center)



**base64图片资源**

base64是表示图片的一种编码形式，浏览器可以直接识别。
base64的优缺点：

- base64编码后的图片的体积会比原始的图片要大
- 一般用于小图片，不合适大图片的转化
- 优点： 不需要发送Ajax请求



### 2.uni-app语法与vue不一致性

## [Class 与 Style 绑定](https://uniapp.dcloud.io/use?id=class-与-style-绑定)

为节约性能，我们将 `Class` 与 `Style` 的表达式通过 `compiler` 硬编码到 `uni-app` 中，支持语法和转换效果如下：

class 支持的语法:

```html
<view :class="{ active: isActive }">111</view>
<view class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }">222</view>
<view class="static" :class="[activeClass, errorClass]">333</view>
<view class="static" v-bind:class="[isActive ? activeClass : '', errorClass]">444</view>
<view class="static" v-bind:class="[{ active: isActive }, errorClass]">555</view>
```

style 支持的语法:

```html
<view v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">666</view>
<view v-bind:style="[{ color: activeColor, fontSize: fontSize + 'px' }]">777</view>
```

非H5端不支持 [Vue官方文档：Class 与 Style 绑定](https://cn.vuejs.org/v2/guide/class-and-style.html) 中的 `classObject` 和 `styleObject` 语法。



### 3.毛玻璃效果：

backdrop-filter    有背景色的话，要设置透明度 rgba()

```
-webkit-backdrop-filter:blur(10px);  ios生效
backdrop-filter: blur(10px);		 安卓生效
```





### 4.微信小程序报错Failed to load local font resource

在做微信小程序开发时引入字体包时报错，[渲染层网络层错误] Failed to load local font resource /iconfont.ttf-do-not-use-local-path-./assets/iconfont/iconfont.wxss&3&7 

 net::ERR_CACHE_MISS 

[![image.png](https://img.uihtm.com/upload/default/20220519/b892742be560642cbca683c84fc4a0f4.png)

[](https://img.uihtm.com/upload/default/20220519/b892742be560642cbca683c84fc4a0f4.png)[![image.png](https://img.uihtm.com/upload/default/20220519/d23cd1530b076d4e4de64f147a01a0be.png)](https://img.uihtm.com/upload/default/20220519/d23cd1530b076d4e4de64f147a01a0be.png)

**原因**：小程序不允许引用本地ttf等字体文件。

**解决办法**：改用网络引用或base64引入，但由于用的是阿里的iconfont字体库，现在下载时没有base64了，可用ttf转base.

**转换 ttf 文件为 base64**: https://transfonter.org/





### 关于获取位置权限

友情提示 用uniapp开发时 需要在 manifest.json -》源码视图-〉mp-weixin 中 增加

"requiredPrivateInfos":[

"getLocation"

],
