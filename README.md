### webpack + TypeScript + Vue2.0 从零开始搭建项目

> npm install
> npm start


> **先创个空白文件夹：

```js
    mkdir learn-vue
    cd learn-vue
```

>新建一个页面`index.html`：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <title>learn-vue</title>
  </head>
  <body>
    <div id="app">
      <p>{{ message }}</p>
    </div>
    <script>
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        }
      })
      </script>
  </body>
</html>
```

这是我们在学习vue的时候会看到的最原始的代码。那我们如何对其进行改造呢。

>根目录建立`package.json`文件，可以手动也可以命令行

```cmd
    npm init -y
```

去掉冗余，简单一点

```json
{
    "name": "learn-vue",
    "version": "1.0.0"
}
```

然后安装webpack 已安装可以忽略

```
npm install --save-dev webpack webpack-cli
```
**会发现目录中增加了`node_modules`**


![Alt text](https://ws3.sinaimg.cn/large/006tNbRwgy1fwm4p2qbkzj304t019745.jpg "node_modules")

> `node_modules`其实就类似于`maven`，这里有个[**有趣的知乎回答**](http://www.baidu.com)

且`package.json`中变成这样了：

```json
{
  "name": "learn-vue",
  "version": "1.0.0",
  "devDependencies": {
    "webpack": "^4.16.5",
    "webpack-cli": "^3.1.0"
  }
}
```

<font color=#9ACD32 size=3>
    webpack是一个js打包工具，使用webpack你可以在一个js文件中使用 import 或者 require 来引用另外一个js文件中定义的组件。不消说，这样你就可以把js组件分文件存放了。
    通过webpack就可以把多个js文件打包成一个js文件。使用了webpack就可以使用import语句来导入别的js文件，这样做有两个好处
</font> 



*  不需要将公共库的js引用写到页面上来了，比如vue的引用就不需要写到`index.html`页面上了。

*  你可以将你的所有js脚本可以按模块来分文件存放了

> vue+webpack后发生了第一次转变：

```js
    npm install npm -g
```

`package.json` 变为：

```json
{
  "name": "learn-vue",
  "version": "1.0.0",
  "devDependencies": {
    "webpack": "^4.16.5",
    "webpack-cli": "^3.1.0",
  },
  "dependencies": {
    "vue": "^2.5.17",
  }
}
```

根目录新建src文件夹，并建立`main.js`

```js
new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue.js!'
    }
})
```

index.html中的`script`语句替换成：

```html
<script src="src/main.js"></script>
```
**并把这句话去掉**

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```
<font color=#89273 size=3>
打开网页还是有hello world的。
 </font>

 > **注意了，接下来就要开始用webpack了：**

 修改main.js脚本，增加这个开头：

 ```js
 import Vue from 'vue/dist/vue.js'

 ```
 根目录新建文件`webpack.config.js`:

 ```js
 var path = require('path');
 
module.exports = {
  mode: 'development',
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
 ```

 >简单的讲解一下这个webpack.config.js

 - mode: 主要用到的模式有production和development。开发时使用development，所以我们现在用development，其实你就算用production对我们的例子也没什么影响。

 - entry: 我嫩之前不是说过使用了webpack之后就可以引用import来导入别的js文件么？那么你可以想象一下，你项目中的js可以构成一个引用树。这个引用树总要有一个树根的，这个树根是不会被任何js所引用的，所有引用最后都可以回溯到它。这里的entry就是webpack的js引用树树根文件

 - output：定义了打包后文件要存放的路径和文件名

 index.html中main.js改成：

 ```js
    <script src="dist/bundle.js"></script>

 ```

根目录新建一个dist文件夹作为output的目录。

```cmd
mkdir dist
```

`npx webpack`后我们能够看到`Hello Vue.js`。

> ### SFC结构：

`src`下新建`components`目录，新建`HelloVue.vue`

```html
<template>
   <p>{{ message }}</p>
</template>
<script>
export default {
  name: 'HelloVue',
  data: function() {
    return {
      message: 'Hello Vue.js!'
    }
  }
}
</script>
```

src下新建App.vue

```html
<template>
   <HelloVue/>
</template>
 
<script>
import HelloVue from './components/HelloVue.vue'
 
export default {
   name: 'App',
   components: {
      HelloVue
   }
}
</script>

```

main.js改为：

```ts
import Vue from 'vue/dist/vue.js'
import App from './App.vue'
 
new Vue({
    el: '#app',
    components: { App },
    template: "<App/>"
})
```

index.html:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>learn-vue</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>

```

**原理：**

```js
index.html是不可能直接使用.vue文件的，
因为浏览器不知道.vue文件是什么。
所以App.vue是通过被某个js调用，
从而被index.html所感知的。
所以它们之间必须要有一个js文件作为连接。
我们使用之前建立的main.js来引用App.vue。
```

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwm5jpbx1jj30om07hjri.jpg)

使用template属性来定义模板 , 使用name属性来注册组件。我们在模板中使用了Hello.vue组件


除此之外，webpack是不认识vue的，所以要添加解析器：


```cmd
npm install --save-dev vue-loader vue-template-compiler
```

并在webpack.config.js中添加：

```js
const { VueLoaderPlugin } = require('vue-loader')
```

```js
plugins: [
    new VueLoaderPlugin()
  ]
``` 

```js
module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      }
    ]
  },
```

最终为：

```js
var path = require('path');
const { VueLoaderPlugin } = require('vue-loader')
 
module.exports = {
  mode: 'development',
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
};
```

#### 使用html-webpack-plugin自动插入脚本

安装：

```js
npm install --save-dev html-webpack-plugin
```

webpack.config.js 中添加：

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
```

```js
plugins: [
    new VueLoaderPlugin(),
   // 以下是HtmlWebpackPlugin的配置
    new HtmlWebpackPlugin({
      template: 'index.html',
      filename: './index.html', // 输出文件【注意：这里的根路径是module.exports.output.path】
      hash: true
    })
  ]
  ```

  到这里就已经可以正常运行了。


  那么如何实现热部署呢？

  安装webpack-dev-server，它可以配置不同的模式来自动更新页面

  ```json
    npm install --save-dev  webpack-dev-server
```

并在package.json中添加：

```js
"scripts": {
    "dev": "webpack-dev-server --inline --progress --config webpack.config.js",
    "start": "npm run dev",
  },
```
npm start/npm run dev就可以运行了，并且更改后会自动更新。


**js改为ts：（typescript）**

安装：

```js
yarn add vue-class-component vue-property-decorator
yarn add ts-loader typescript --dev
```

- [vue-class-component](https://github.com/vuejs/vue-class-component)：强化 Vue 组件，使用 TypeScript/装饰器 增强 Vue 组件
- [vue-class-component](https://github.com/kaorun343/vue-property-decorator)：在 vue-class-component 上增强更多的结合 Vue 特性的装饰器

webconfig.config.js中添加：

```js
    resolve: {
    extensions: ['.js', '.vue', '.json', '.ts'],
    alias: {
      '@': path.resolve(__dirname, '..', 'src'),
    }
  },
module: {
    rules: [
      {
        test: /\.tsx?$/,
        loader: 'ts-loader',
        exclude: /node_modules/,
        options: {
          appendTsSuffixTo: [/\.vue$/],
        }
      },
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      }
    ]
  },

```

根目录添加

tsconfig.json：

```json
{
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules"
  ],
  "compilerOptions": {
    "allowSyntheticDefaultImports": true,
    "experimentalDecorators": true,
    "allowJs": true,
    "module": "esnext",
    "target": "es5",
    "moduleResolution": "node",
    "isolatedModules": true,
    "lib": [
      "dom",
      "es5",
      "es2015.promise"
    ],
    "sourceMap": true,
    "pretty": true
  }
}

```

在 ./src 目录创建 vue-shim.d.ts 文件，让 ts 识别 .vue 文件：

```ts
// vue-shim.d.ts
declare module "*.vue" {
  import Vue from "vue";
  export default Vue;
}
```
main.js改为main.ts，
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fwm5uww7a6j309l00zq2q.jpg)

改造之后的 ts 文件不会识别 .vue 文件，所以在引入 .vue 文件的时候，需要手动添加 .vue 后缀

webpack.config.js中的main.js也改为main.ts!

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fwm5vldbhlj30810123ya.jpg)

App.vue改造为：

```HTML
<template>
  <div id="app">
    <hello1/>
  </div>
</template>
 
<script  lang="ts">
import Vue from 'vue'
import hello from './components/HelloVue.vue'
import Component from 'vue-class-component'
@Component({
    components: {
        'hello1': hello
    }
})
export default class App extends Vue{
}
</script>
```

HelloVue.vue改造为：

```HTML
<template>
    <p>{{message}}</p>
</template>
 
 
<script lang="ts">
  import Vue from 'vue'
  import Component from 'vue-class-component'
 
  @Component
  export default class App extends Vue {
    message="hello World!"
  }
</script>
 
```


webpack.config.js最终改造为：


```JS

var path = require('path');
 
const { VueLoaderPlugin } = require('vue-loader')
const HtmlWebpackPlugin = require('html-webpack-plugin')
 
module.exports = {
  mode: 'development',
  entry: './src/main.ts',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  resolve: {
    extensions: ['.js', '.vue', '.json', '.ts'],
    alias: {
      '@': path.resolve(__dirname, '..', 'src'),
    }
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        loader: 'ts-loader',
        exclude: /node_modules/,
        options: {
          appendTsSuffixTo: [/\.vue$/],
        }
      },
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin(),
 
    new HtmlWebpackPlugin({
      template: 'index.html',
      filename: './index.html',
      hash: true
    })
  ]
};

```


运行得到hello world!

小小demo