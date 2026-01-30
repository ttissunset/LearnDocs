#### 1. 创建 package 文件夹，作为包的主入口并创建组件

##### 注意：一定需要导出 name 属性，将作为使用组件的标志

```javascript
<script>
export default {
  name: "kiri-verify",
};
</script>

// 使用：<kiri-verify/>
```

#### 2.  package 下创建 index.js，用于全局注册组件

![image-20250821153831632](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/sunbox/image-20250821153831632.png)

#### 3. 编辑 index.js 

```javascript
import { defineAsyncComponent } from "vue";

const components = import.meta.glob("./项目名称/*.vue");

const registerGlobalComponent = function (app) {
  for (const [key, value] of Object.entries(components)) {
    const name = key.slice(key.lastIndexOf("/") + 1, key.lastIndexOf("."));
    app.component(name, defineAsyncComponent(value));
  }
};

export default registerGlobalComponent;
```

![image-20250821153855672](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/sunbox/image-20250821153855672.png)

#### 4. 本地测试

- 在 `main.js` 注册

```javascript
import { createApp } from "vue";
import App from "./App.vue";
import registerGlobalComponent from "../package/index";    

const app = createApp(App);
registerGlobalComponent(app);
app.mount("#app");
```

- 在 `App.vue` 中使用

```javascript
<template>
  <kiri-verify :verifyWidth="400" :verifyHeight="300" :tolerance="2"></kiri-verify>
</template>
```

#### 5. 修改 vite.config.js 配置文件

```javascript
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import path from "path";

// https://vite.dev/config/
export default defineConfig({
  plugins: [vue()],
  // rollup打包配置
  build: {
    outDir: "kiri-ui-package", //输出文件名称
    lib: {
      entry: "./package/index.js", //指定组件编译入口文件
      name: "kiri-ui-package",
      fileName: "kiri-ui-package",
    }, //库编译模式配置
    rollupOptions: {
      // 确保外部化处理那些你不想打包进库的依赖
      external: ["vue"],
      output: {
        // 在 UMD 构建模式下为这些外部化的依赖提供一个全局变量
        globals: {
          vue: "Vue",
        },
      },
    }, 
  },
});

```

#### 5. 发布包

- 运行 `npm run build` 打包组件
- 在输出目录下 `npm init -y` 初始化配置文件
- 修改 `package.json` 配置文件

```javascript
{
  "name": "kiri-ui-package",
  "version": "1.0.0",
  "main": "kiri-ui-package.js",
  
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "kiri",
    "Slide_Verification_Code",
    "Verify"
  ],
  "author": "kiri",
  "license": "ISC",
  "description": "拼图验证码1.0版本"
}
```

- 在输出目录下 `npm adduser` 登录 `npm` 账号 或者 `npm login` 
- 执行 `npm publish --access=public` 发布包
- 如果需要更新包，修改 `package.json` 的 `version` 版本号重新发布

#### 6. 使用包

- 执行 `npm i kiri-ui-package` 下载组件包
- 在 `main.js` 注册组件

```javascript
import 'kiri-ui-package/kiri-ui-package.css'
import kr from 'kiri-ui-package'
app.use(kr)
```

- 组件中使用

```javascript
<kiri-verify width="400" height="300" tolerance="2"></kiri-verify>
```

