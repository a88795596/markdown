# VUE-CLI中使用CSS样式全局注入

## CLI 2.X+中使用

插件安装

> `npm i style-resources-loader -D`

### 样例代码

```javascript
// webpack.config.js
module.exports = {
  ...
  module: {
    rules: [{
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader', {
            loader: 'style-resources-loader',
            options: {
                patterns: path.resolve(__dirname, 'path/to/less/variables/*.less')
            }
        }]
    }]
  }
```

## CLI 3.X+中使用

> `vue add style-resources-loader`

### 安装流程

安装过程中会出现选择CSS预编译语言，选择自己项目使用的预编译语言回车确认

### 编辑配置

打开项目中的 `vue.config.js`，可以看到如下代码

```JavaScript
module.exports = {
  ...
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'scss',
      patterns: []
    }
  }
}

```

```JavaScript

const path = require("path");

module.exports = {
  ...
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'scss',
      // 数组形式配置需要载入的文件路径
      patterns: [
        path.resolve(__dirname, "src/style/color.scss")
      ]
    }
  }
}
```

### 注意项

1. 配置路径不能使用别名
2. 更新了文件后需要重启项目
