```javascript
// 引入 defineConfig 获得类型提示（Vue CLI 新版内置，无需额外安装）
const { defineConfig } = require('@vue/cli-service');
// 引入内置的 terser-webpack-plugin（直接用，无需 npm install）
const TerserPlugin = require('terser-webpack-plugin');

module.exports = defineConfig({
  // 可选：生产环境关闭 sourceMap，避免源码暴露，同时减小打包体积
  productionSourceMap: false,
  // webpack 链式配置（推荐，精准控制生产环境）
  chainWebpack: (config) => {
    // 仅对生产环境生效，开发环境保留所有 console
    if (process.env.NODE_ENV === 'production') {
      // 配置 terser 压缩插件，移除 console 和 debugger
      config.optimization.minimizer('terser').tap((args) => {
        // 扩展 terser 压缩配置
        args[0].terserOptions.compress = {
          ...args[0].terserOptions.compress,
          drop_debugger: true, // 强制移除所有 debugger 语句
          drop_console: true,  // 开启 console 剔除开关
          // 精准控制：仅移除 log/info，保留 error/warn（生产环境排错必备）
          pure_funcs: ['console.log', 'console.info']
        };
        return args;
      });
    }
  }
});
```

