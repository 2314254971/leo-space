# cesium

## 1.通过安装库来集成

```bat
#安装cesium
npm install  cesium --save
#安装插件（非必须）
npm i vite-plugin-cesium vite -D

```

配置vite.config.js

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import cesium from 'vite-plugin-cesium'//上述插件

export default defineConfig({
    plugins: [vue(),cesium()],//在原基础上加上cesium，在方括号里面添加即可
})
```

上述完成后即可正常使用了

例如新建页面map.vue

```vue
<script setup>
import { Viewer } from 'cesium';
import { ref } from 'vue'
import * as Cesium from 'cesium';
onMounted(() => {const viewer = new Cesium.Viewer("cesiumContainer");})
</script>

<template>
  <div id="cesiumContainer" ></div>
</template>

<style scoped lang="scss">

</style>
```

上述页面创建了一个viewer窗口（注意：一定要onmount，因为script setup代码会优先执行，那个时候dom还没构建玩窗体div）

参考：[vue3+vite搭建第一个cesium项目详细步骤及环境配置（附源码）_cesium vue3 vite-CSDN博客](https://blog.csdn.net/whs15193553607/article/details/140229168)



## 2.加载本地cesium

参考：[vite.config.js · Rxiaou/cesium_ry_v3 - 码云 - 开源中国 (gitee.com)](https://gitee.com/ren-xiaoyou/cesium_ry_v3/blob/master/vite.config.js)

参考该文件的vite config文件配置

（个人理解：好像还是会把源文件拷贝到nodemodule文件夹下进行调用，如果不改变源码的话还是推荐安装库更为快捷）

