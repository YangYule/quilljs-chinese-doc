---
layout: docs
title: Adding Quill to Your Build Pipeline
permalink: /guides/adding-quill-to-your-build-pipeline/
---

Quill的每个版本都可以从各种来源构建并使用，包括[NPM](https://www.npmjs.com/package/quill)或其[CDN](/docs/download/)。然而，可能会出现用户希望从源代码构建Quill的用例，作为应用程序构建管道的一部分。如果这个愿望没有发生在你身上，请不要冒汗！使用预建版本是使用Quill最简单的方法。

Quill使用Webpack构建，本指南主要针对[Webpack](https://webpack.js.org/concepts/)用户。但是有些原则可能会转化为其他构建环境。

本指南涵盖的所有内容的最小工作示例可以在[quill-webpack-example](https://github.com/quilljs/webpack-example/)中找到。

### Webpack

您将需要将Webpack和适当的加载器作为开发依赖项添加到您的应用程序。如果您想从源代码生成Parchment，则Typescript编译器也是必需的。

- Quill源码 - [`babel-core`](https://www.npmjs.com/package/babel-core), [`babel-loader`](https://www.npmjs.com/package/babel-loader), [`babel-preset-es2015`](https://www.npmjs.com/package/babel-preset-es2015)
- Parchment源码 - [`ts-loader`](https://www.npmjs.com/package/ts-loader), [`typescript`](https://www.npmjs.com/package/typescript)
- SVG icons - [`html-loader`](https://www.npmjs.com/package/html-loader)

你的Webpack配置文件还需要引用（alias）Quill和Parchment来指向它们各自的入口源文件。没有这个，Webpack将使用NPM中包含的预建文件，而不是从源代码构建。

### 入口

Quill被编译为了`quill.js` and `quill.core.js`。用于构建[quill.js](https://github.com/quilljs/quill/blob/master/quill.js)和[core.js](https://github.com/quilljs/quill/blob/master/core.js)的入口文件的目的是导入和注册必要的依赖关系。您可能希望在应用程序中使用类似的入口点，其中只包含您使用的格式，模块或主题。

```js
import Quill from 'quill/core';

import Toolbar from 'quill/modules/toolbar';
import Snow from 'quill/themes/snow';

import Bold from 'quill/formats/bold';
import Italic from 'quill/formats/italic';
import Header from 'quill/formats/header';


Quill.register({
  'modules/toolbar': Toolbar,
  'themes/snow': Snow,
  'formats/bold': Bold,
  'formats/italic': Italic,
  'formats/header': Header
});


export default Quill;
```


### 样式表

从源代码编译CSS并不能获取多少好处，因为CSS很容易被覆盖，但是在你的CSS文件中用[`css-loader`](https://www.npmjs.com/package/css-loader)的波浪线前缀包含Quill的样式或许是有用的。

```css
@import "~quill/dist/quill.core.css"

// 剩余的css
```


### 例子

从[quill-webpack-example](https://github.com/quilljs/webpack-example/) 可以查看一个最小的工作样例。
