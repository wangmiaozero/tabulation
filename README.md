# data

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

一、生成规则说明

1、每个字段配置有最小列数和最大列数，字段label宽度固定为2列（将表格等分为14列，1列为1单位）。

2、当前行列数不满14时，优先扩展可扩展的字段（即有最大列数的字段），可扩展字段扩展之后仍不满14时，扩展当前行最后一个字段。

二、关键点

1、生成行，并计算下一行跨行字段所占列数和

1、跨行处理，上一行（当前行之前的行）存在跨行字段时，当前行可用列数=总列数-跨行字段所占列数和