

# 微信小游戏开发之Cocos Creator多分辨率场景适配方案



## 主题

Cocos Creator不同手机分辨率的背景图像和场景内容适配

## 特别说明

CocosCreator微信小游戏开发系列文章，是我在逐步开发过程中，基于官方文档之上，记录一些重点内容，以及对官方文档中有些知识点的补充和分析。

## 正文

我在《微信小游戏开发之Cocos Creator系列文章》中，写Cocos Creator项目配置时，提到了Canvas默认分辨率设置，微信推荐使用的设计稿分辨率是750x1334（iphone6的屏幕尺寸），然后把模拟器的分辨率尺寸也设成了750x1334。

![img](https:////upload-images.jianshu.io/upload_images/4982095-dcb99376d1548e25.png?imageMogr2/auto-orient/strip|imageView2/2/w/1166/format/webp)

项目预览设置

当我发布构建，在微信开发者工具运行时，哦豁，怎么会有黑边呢，突然想起来现在市面上的手机分辨率五花八门的，肯定要做屏幕多分辨率适配的啊，大意了啊，我没有闪。

按照惯例，先看下当设计分辨率和屏幕分辨率出现差异时，Cocos Creator 官方建议如何进行适配呢？

有兴趣的可以去看下cocos官方文档《[多分辨率适配方案](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.cocos.com%2Fcreator%2Fmanual%2Fzh%2Fui%2Fmulti-resolution.html)》的详细介绍，我直接总结一下官方文档中的适配方案：

文档中的适配主要是靠Canvas组件节点上的两个选项：

- `适配高度（Fit Height）`
- `适配宽度（Fit Width）`

下面是这两个选项适合使用的情形：

1. `设计分辨率宽高比`大于`屏幕分辨率宽高比`，勾选`Fit Height`，可以避免屏幕可见区域内出现黑边，之后配合Widget（对齐挂件）调整 UI 元素的位置，来保证 UI 元素出现在屏幕可见区域内。
2. `设计分辨率宽高比`小于`屏幕分辨率宽高比`，勾选`Fit Width`，可以避免出现黑边，同样需要配合Widget（对齐挂件）来调整 UI 元素的位置，使UI 元素出现在屏幕可见区域内。
3. 比例相同的情况，随便勾选哪一个都可以。

为什么是`设计分辨率宽高比`大于`屏幕分辨率宽高比`时，适配高度而不是适配高度呢？

来看看它的适配过程:

假设设计分辨率宽高是`800 x 480`，屏幕分辨率宽高是`1024 x 768`



```bash
1. 先算以下两个值：
    * A1: 屏幕分辨率宽 / 设计分辨率宽 = 1024 / 800 = 1.28
    * A2: 屏幕分辨率高 / 设计分辨率高 = 768 / 480 = 1.6
   
2. 适配实际是将场景图像放大 "A1或A2" 倍
    
3. 假设放到A1倍：分辨率变成 800*1.28 / 480*1.28 = 1024 / 614.4
可以看到高度其实还没有达到当前屏幕的高度，所以还是会出现黑边

4. 放到A2倍：分辨率变成 800*1.6 / 480*1.6 = 1280 / 768
可以看到宽高都达到当前屏幕宽高度，
只是宽度有部分超出屏幕被裁剪掉了，
但不会出现黑边

5. 所以，设计分辨率宽高比大于屏幕分辨率时，适配高度。
```

同理，如果是`设计分辨率宽高比`小于`屏幕分辨率宽高比`，就应当适配宽度。

文档中还提到了其他的几种适配策略，但是他们都有可能出现黑边：

- SHOW_ALL模式：同时勾选`Fit Height`和`Fit Width`，图像内容不会失真，场景图像等比进行缩放，缩放比例是取`屏幕宽/设计分辨率宽`与`屏幕高/设计分辨率高`中较小的一个值。
- NO_BORDER模式：`Fit Height`和`Fit Width`两个都不勾选，图像内容不会失真，但会有裁剪，场景图像等比进行缩放，缩放比例是取`屏幕宽/设计分辨率宽`与`屏幕高/设计分辨率高` 中较大的一个值。
- EXACT_FIT模式：不详细介绍了，官方也是一笔带过。

看到这里，你会发现因为手机屏幕的分辨率实在太多，`屏幕分辨率宽高比`大于小于`设计分辨率宽高比`的手机屏幕都会有，所以在编辑器上勾选`Fit Height`和`Fit Width`中的某一个或者多个，都没有办法适配所有屏幕，那应该怎么办呢？

### 方案一：动态选择启用 Fit Height 模式和 Fit Width 模式

既然编辑器上怎么勾选都会有问题，那我们可以动态地判断`屏幕分辨率宽高比`来选择启用`Fit Height`模式和`Fit Width`模式啊。

直接上代码：



```javascript
//FullScreenAdapter.js
cc.Class({
    extends: cc.Component,

    onLoad () {
        //监听窗口大小变化时的回调，每次窗口变化都要自动适配
        cc.view.setResizeCallback(() => this.screenAdapter());
        this.screenAdapter();
    },

    /**
     * Fit Height 模式：适用于宽大于高的屏幕
     * Fit Width 模式：适用于高大于宽的屏幕
     */
    screenAdapter() {
        //当前屏幕分辨率比例
        let screenRatio = cc.winSize.width / cc.winSize.height;
        //设计稿分辨率比例
        let designRatio = cc.Canvas.instance.designResolution.width / cc.Canvas.instance.designResolution.height;
        
        if (screenRatio <= 1) {
            //屏幕高度大于或等于宽度,即竖屏
            if (screenRatio <= designRatio) {
                this.setFitWidth();
            } else {
                //此时屏幕比例大于设计比例
                //为了保证纵向的游戏内容不受影响，应该使用 fitHeight 模式
                this.setFitHeight();
            }
        } else {
            //屏幕宽度大于高度,即横屏
            this.setFitHeight();
        }
    },

    setFitWidth() {
        cc.Canvas.instance.fitHeight = false;
        cc.Canvas.instance.fitWidth = true;
    },

    setFitHeight() {
        cc.Canvas.instance.fitHeight = true;
        cc.Canvas.instance.fitWidth = false;
    }
});
```

把上面的js脚本挂载到Canvas节点上，就可以简单的实现所有屏幕适配了。

**注意：** 如果项目运行在可动态调整窗口大小的平台（比如浏览器），最好每次调整窗口都刷新一下页面。

### 方案二：基于SHOW_ALL模式，动态设置最大父节点的scale属性

这个方案比较麻烦，但是确实可以实现所有屏幕的适配，大家有兴趣可以去看看“[Cocos Creator 多分辨率完美适配](https://www.jianshu.com/p/24cba3de1e33)”系列文章。

## 刘海屏和水滴屏等手机状态栏的适配

现在市面上的手机额头上千奇百怪的状态，什么刘海啊、水滴啊、挖孔啊、伸缩啊，为了用户体验，没办法我们也要去适配它。

方法很简单，我们只要获取微信菜单按钮（右上角胶囊按钮）距离屏幕顶部的距离，然后设置一下顶部节点的paddingTop就可以了，直接上代码吧：



```javascript
let menuInfo = wx.getMenuButtonBoundingClientRect();
let systemInfo = wx.getSystemInfoSync();
let paddingTop = this.node.parent.height * (menuInfo.top / systemInfo.screenHeight);

let widget = this.node.getComponent(cc.Widget);
widget.top = paddingTop;
widget.isAbsoluteTop = true;
widget.isAlignTop = true;
widget.updateAlignment();
```

### 介绍cocos提供的几个获取View的方法



```css
cc.view.getDesignResolutionSize()
　　　获取的是你在编辑器中设计的分辨率，也就是canvas 组件下设置的设计分辨率。

cc.view.getFrameSize()
　　　获取各种手机、pad上的屏幕分辨率，也就是硬件分辨率。

cc.view.getVisibleSizeInPixel()
　　　获取的是 visibleSize 的基础上乘以各种适配策略下的缩放比例后的分辨率。

cc.view.getVisibleSize()
　　　返回视图窗口可见区域尺寸
```

## 总结

多分辨率适配的核心原理是动态改变Canvas节点或者其他节点的scale属性，熟悉Cocos Api文档的各个方法，能为我们解决各种疑难问题提供丰富的思路。



作者：Android_Cloud
链接：https://www.jianshu.com/p/771966e6a13c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。