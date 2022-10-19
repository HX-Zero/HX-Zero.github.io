# vue动态更改图片的src地址

html:

```
<img ref="study" :src="studyDataPic">
```

script:

```
data: {
	return {
		studyDataPic: require('@/assets/images/school.png')
	}
}
```

更改图片地址：

```
this.studyDataPic = require('@/assets/images/load.gif')
```

#### 注意：

1.必须使用require
因为动态添加src被当做静态资源处理了，没有进行编译，所以要加上require
2.require里面的地址，要使用单引号