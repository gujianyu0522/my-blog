```js
// vite.config.js
import { fileURLToPath, URL } from "node:url";
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3120/',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  },
  plugins: [react()],
  resolve: {
    alias: {
        /* 配置@，这样在代码里可以畅快的引入文件 */
      "@": fileURLToPath(new URL("./src", import.meta.url)),
    },
  },
})
```

```json
// package.json
{
    /* 约束node和npm的最低版本 */
    "engines": {
        "node": ">=16.17.0",
        "nom": ">=8.15.0"
    }
}

```



作者：闲D阿强
链接：https://juejin.cn/post/7167701003976835102
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。