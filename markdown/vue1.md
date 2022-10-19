# vue3 setup使用i18n插件(多语言)

### 一、安装

```js
 npm install vue-i18n@next
复制代码
```

OR

```js
yarn add vue-i18n@next
复制代码
PS：我的版本是 9.1.6
```

### 二、使用

#### 1.在`src`目录新建 `language`文件夹 （如下图）

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbeb371e14244b97b64cdadda9840b4e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

```
PS：此处我用的 TS 如果没有安装 TS 的可以使用 JS
```

#### 2.在 `language` 文件夹下新建 `index.ts` `en-US.ts` `zh-CN.ts` 三个文件

```js
//en-US.ts
export default  {
  // 名称
  'name': 'peng-xiao-hei' 
}
复制代码
//zh-CN.ts
export default  {
  // 名称
  'name': '彭小黑' 
}
复制代码
PS：此处使用了 VITE 的 import.meta.globEager。非 VITE 的 可以使用 require.context
//index.ts
import { createI18n } from 'vue-i18n'		//引入vue-i18n组件
//引入同级目录下文件
const modules = import.meta.globEager('./*')

//假设你还有其他目录下的语言文件 它的路径是 src/views/home/locales/en-US.ts
//那么你就可以 使用 :lower:（小写） :upper:（大写） 来引入文件
const viewModules = import.meta.globEager('../views/**/locales/[[:lower:]][[:lower:]]-[[:upper:]][[:upper:]].ts')

function getLangAll(): any{
  let message:any = {}
  getLangFiles(modules,message)
  getLangFiles(viewModules,message)
  return message
}
/**
 * 获取所有语言文件
 * @param {Object} mList
 */
function getLangFiles(mList:any,msg:any){
  for(let path in mList){、
    if(mList[path].default){
      //  获取文件名
      let pathName = let pathName = path.substr(path.lastIndexOf('/') + 1,5)
      
      if(msg[pathName]){
        msg[pathName] = {
          ...mList[pathName],
          ...mList[path].default
        }
      }else{
        msg[pathName] = mList[path].default
      }
      
    }
  }
}

  //注册i8n实例并引入语言文件
 const i18n = createI18n({
    legacy: false,
    locale: 'zh-CN',
    messages: getLangAll()
  })
  
  export default i18n; //将i18n暴露出去，在main.js中引入挂载
复制代码
```

#### 3.main 注册

```js
//main.ts
import VueI18n from './language'

let app = createApp(App);
app.use(VueI18n)
复制代码
```

#### 4. 页面使用

```js
//home.vue
<template>
    <div>{{t('name')}}</div>
</template>

<script>
import { useI18n } from "vue-i18n";

export default defineComponent({
    setup() {
        const { t } = useI18n();
        return{
            t
        }
   }
})
</script>
```