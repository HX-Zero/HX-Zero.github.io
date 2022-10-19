# [利用CSS hover伪类改变其他元素的总结](https://www.cnblogs.com/engeng/p/7429095.html)

:hover 伪类经常用于页面的一些鼠标交互、链接点击变化，增强页面的用户体验，但是可以用来改变其他元素样式，可以在不使用JS 的情况下，达到想要的页面效果。

**1、hover改变自身的效果：**

鼠标悬浮改变样式：

![img](https://images2017.cnblogs.com/blog/1038573/201708/1038573-20170825165925949-501337504.gif)

HTML

```
<div id="yanshi">
            演示
        </div>
```

CSS

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
　　　　　　　#yanshi{
                width: 100px;
                height: 100px;
                transition: background-color 0.5s,color 0.5s;
                text-align: center;
                margin: 20px auto;
                line-height: 100px;
                border: 1px solid green;
                
            }
            #yanshi:hover{
                background-color: green;
                color: white;
                
            }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**2、hover改变子元素的样式**

![img](https://images2017.cnblogs.com/blog/1038573/201708/1038573-20170825170914183-406600658.gif)

HTML

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
        <div id="fir">
            <div class="se"></div>
            <div class="se"></div>
            <div class="se"></div>
            <div class="se"></div>
            <div class="se"></div>
            <div class="se"></div>
            <div class="se"></div>
        </div>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

CSS

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
            #fir{
                width: 820px;
                height: 300px;
                border: 1px solid green;
                margin: 5px auto;
                
            }
            #fir div{
                border: 1px dashed gray;
                width: 100px;
                height: 100px;
                float: left;
                margin: 5px;
                transition: transform 0.5s;
            }
            
            
            div#fir:hover .se{
                transform: rotate(15deg);
            }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

hover直接可以批量改变内部元素的样式，这样的效果很多用在产品的展示页面，鼠标悬浮在页面的一个元素上，不同的产品图片实现位移，达到很好的交互效果。重要的一点，hover在父元素上，对子元素进行样式调整。

 

**3、hover改变兄弟元素的样式：（需要在hover之后添加“+”）**

HTML不变，变化的CSS部分

```
            #fir div:first-child:hover + .se{
                transform: rotate(15deg);
            }
```

效果：

![img](https://images2017.cnblogs.com/blog/1038573/201708/1038573-20170825172355589-1949411037.gif)

可以看到，只有一个矩形应用到了样式，hover对兄弟元素改变样式，只对相邻的兄弟元素起作用。

 

总结一下：hover对同级别的元素改变样式，改变的是相邻兄弟元素的样式，即一个元素的样式；对元素的子元素应用hover改变样式，可以同时起作用。利用伪类改变其他元素的样式，其他元素须是hover元素的子元素。