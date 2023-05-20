# Vite配置
## plugins插件

#### legacy() 浏览器兼容
##### 示例
```bash
npm install @vitejs/plugin-legacy -D
```
```js
import { defineConfig } from 'vite';
import legacy from '@vitejs/plugin-legacy';

export default defineConfig({
  plugins: [
    legacy({
      targets: ['ie >= 11'],
      additionalLegacyPolyfills: ['regenerator-runtime/runtime'],
      polyfills: ['es.symbol'],
    }),
  ],
});
```
在上面的示例中，我们指定要支持的浏览器版本为IE11及以上版本。我们还指定了要在转换过程中使用的附加polyfills，以及要添加到legacy bundle中的polyfills。此外，您可以通过配置additionalLegacyPolyfills属性来添加其他的polyfills。
#### vitePluginImp 实现组件的按需导入
##### 示例
```bash
npm install vite-plugin-imp -D
```
```js
import vitePluginImp from 'vite-plugin-imp'

export default {
  plugins: [
    vitePluginImp({
      libList: [
        {
          libName: 'ant-design-vue',
          style: (name) => `ant-design-vue/es/${name}/style/index.css`
        }
      ]
    })
  ]
}
```
在上面的示例中，我们指定了要按需加载的第三方组件库的名称为'ant-design-vue'，并且指定了样式文件的路径为'ant-design-vue/es/${name}/style/index.css'。这意味着，当您在代码中使用'ant-design-vue'组件库中的某个组件时，该组件库的样式文件会被自动加载到您的项目中。





## build构建选项

#### build.minify

#### build.target

#### build.reportCompressedSize


#### build.sourcemap

