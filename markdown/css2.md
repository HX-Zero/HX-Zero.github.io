比如计算一个带刘海屏高度的方法

在uni-app中 

```css
.dpBox {
			position: absolute;
			top: calc(var(--status-bar-height) + 44px);
			right: 8px;
		}
```

关键点calc(var(--status-bar-height) + 44px);

加号两边是有空格的