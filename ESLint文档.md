# ESLint

ESLint 是在 ECMAScript/JavaScript 代码中识别和报告模式匹配的工具，它的目标是保证代码的一致性和避免错误。

英文文档地址：[https://eslint.org/](https://eslint.org/)
中文文档地址：[https://eslint.bootcss.com/](https://eslint.bootcss.com/)

## 安装

1. 新建项目，并在目录下初始化npm（如果没有）：
```sh
npm init -y
```

2. 安装ESLint（推荐本地安装）
```sh
# 本地安装
npm install eslint --save-dev
# 或全局安装：
npm install eslint --global
```

3. 初始化配置文件
```sh
./node_modules/.bin/eslint --init  # Windows下路径需要使用反斜杠
# 或
eslint --init
```

配置选项：
```sh
How would you like to use ESLint? · problems
What type of modules does your project use? · esm
Which framework does your project use? · none
Does your project use TypeScript? · No
Where does your code run? · browser, node
What format do you want your config file to be in? · JavaScript
```

生成`.eslintrc.js`配置文件：

```js
module.exports = {
    "env": {
        "browser": true,
        "es2021": true,
        "node": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "ecmaVersion": 12,
        "sourceType": "module"
    },
    "rules": {
    }
};
```
如果之前在初始化时未指定`node`环境，则需要在`env`属性中定义`node`的全局变量和作用域，或者指定`module`为全局变量，否则`eslint:recommended`的默认规则会报错：`'module' is not defined  no-undef`：

```js
module.exports = {
    "env": {
        // ...
        "node": true
    },
    // ...
    // 或
    "globals": {
        "module": true
    }
};
```

也可以自行创建和配置`.eslintrc.js`文件。

## 运行

首先，创建`src`目录，并创建脚本文件`src/index.js`：

```js
console.log('eslint');;    // 多加一个分号
```

在`package.json`的`scripts`中配置脚本命令：

```js
{
    // ...
    "scripts": {
        // ...
        "eslint": "eslint ./**"                  // 检查当前路径下的文件
        // 或
        "eslint": "eslint ./src/**"              // 只检查src路径下的文件
        // 或
        "eslint": "eslint --ext .js,.vue src"    // 只检查src目录下的.js文件和.vue文件
    }
}
```

创建`.eslintignore`文件，指定ESLint检查时忽略的文件和目录（如果有需要）：
```
/dist/
/*.js
/*.json
```

除此之外，为了指定ESLint检查和忽略的文件和目录，可以在eslint命令中设置，详见官方ESLint命令行文档：[https://eslint.bootcss.com/docs/user-guide/command-line-interface](https://eslint.bootcss.com/docs/user-guide/command-line-interface)


运行命令：

```sh
npm run eslint
```

运行结果会报错，提示有不必要的分号：
```sh
error  Unnecessary semicolon  no-extra-semi
```

如果需要eslint运行的时候自动修复（能够被修复的）问题，可以在`package.json`中添加命令：
```js
{
    // ...
    "scripts": {
        // ...
        "eslint": "eslint ./**"    
        "eslint:fix" "eslint ./** --fix"    // --fix表示修复问题
    }
}
```

运行命令：

```sh
npm run eslint:fix
```

原本代码中多余的分号被删除。

## VS Code安装ESLint插件
编写完代码，再运行命令检查语法问题是繁琐的，而且有些问题应该在编码阶段就被发现和修复。对此，可以在VS Code中搜索和安装ESLint插件，该插件会检测和应用项目中的`.eslintrc.js`，并且对不符合规则的代码作出提示，还能对错误进行修改。

![img](./images/eslint/01.png)

![img](./images/eslint/02.png)

如果需要在保存文件的时候自动根据规则修复，可以在`settings.json`文件中添加：

```json
{
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    }
}
```

打开`settings.json`：File->Preferences->Settings->Text Editor->Code Actions On Save-> Edit in settings.json

## 配置项

参考官方文档的配置项：[https://eslint.bootcss.com/docs/user-guide/configuring](https://eslint.bootcss.com/docs/user-guide/configuring)

之前的配置文件：
```js
module.exports = {
    "env": {
        "browser": true,
        "es2021": true,
        "node": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "ecmaVersion": 12,
        "sourceType": "module"
    },
    "rules": {
    }
};
```

### env
`env`定义了预定义的全局变量，例如：`"node": true`表示使用Node的全局变量，更多可用的环境参考官方文档。

### extends
`extends`表示ESLint应用的规则的集合，值可以是字符串，也可以是字符串的数组，扩展的来源可以是三种：
1. ESLint内置的扩展。例如：`eslint:recommended`和`eslint:all`，其中，`eslint:recommended`是官方推荐的规则；
2. 可共享的扩展。通过npm提供的一些共享的包，是一些流行的风格指南/编码规范，例如`standard`和`airbnb`，包名一般带`eslint-config-`前缀。可以手动通过npm安装，安装后会提示安装其他相关的包，按提示安装即可：
```sh
npm install eslint-config-standard --save-dev
npm install eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-plugin-standard --save-dev
```
修改配置文件：

```js
module.exports = {
    // ...
    "extends": [
        "standard"
    ]
};
```

除此之外，在运行`eslint --init`初始化配置文件时，可以直接从几个流行风格中指定和安装其中一个：

```sh
How would you like to use ESLint? · style
# ...
How would you like to define a style for your project? · guide
Which style guide do you want to follow? · standard
```

3. 插件。参考下面要讲的插件属性。

### plugins
虽然有多种官方的扩展可以选择，但是默认的扩展只能检查JavaScript语法，不能解析JSX或者Vue文件，对此，我们需要安装相关的插件。

```sh
npm install eslint-plugin-vue --save-dev
```

在配置文件中指定插件以及使用的扩展规则：

```js
module.exports = {
    // ...
    "plugins": [
        "vue"
    ],
    "extends": [
        "standard",
        "plugin:vue/essential"
        // 或
        "plugin:vue/recommended"
    ]
};
```

也可以在`eslint --init`初始化配置文件时配置：

```sh
How would you like to use ESLint? · style
# ...
Which framework does your project use? · vue
```

验证插件是否安装成功，首先创建并初始化`src/test.vue`文件：

```html
<template>
    <div></div>
</template>

<script>
export default {
    data() {
        return {
            test: "double"
        }
    }
}
</script>

<style scoped>

</style>
```

运行`npm run eslint`进行检查，或者已经在VS Code上安装了ESLint的插件，可以看到
报了若干错误，说明插件安装成功。

### 解析器配置：
```js
module.exports = {
    // ...
    "parserOptions": {
        // es版本号，6表示2015，12表示2021
        "ecmaVersion": 12,
        // 代码类型，"script"（默认）或"module"
        "sourceType": "module"，
        // 语言特性
        "ecmaFeatures": {
            "globalReturn": true,  // 允许在全局作用域下使用return语句
            "impliedStrict": true, // 启用全局 strict mode 
            "jsx": true            // 启用 JSX
        }
    }
};
```

### 解析器

### rules
有时扩展中的规则不是我们想要的，可以通过`rules`修改规则。ESLint中附带大量的规则，参考官方文档：[https://cn.eslint.org/docs/rules/](https://cn.eslint.org/docs/rules/)。要改变一个规则设置，你必须将规则 ID 设置为下列值之一：
* "off" 或 0 - 关闭规则
* "warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
* "error" 或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)

例：
```js
module.exports = {
    // ...
    "rules": {
        "eqeqeq": "off",    // 关闭全等检查
        "quotes": ["error", "double"]    // 强制使用双引号，否则报错
    }
};
```

## 在 Webpack 中使用 ESLint

1. 安装相关的包

```sh
npm install eslint eslint-loader eslint-friendly-formatter --save-dev
```

2. 修改 webpack 开发环境的配置文件 `webpack.dev.js`：

```js
const path = require('path')

module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: 'eslint-loader',
                enforce: 'pre',
                include: path.resolve(__dirname, 'src'),
                options: {
                    formatter: require('eslint-friendly-formatter')
                }
            }
        ]
    }
}
```

3. 创建 `.eslintrc.js` 配置文件：

```js
module.exports = {
    root: true,
    parserOptions: {
        parser: 'babel-eslint',
        sourceType: 'module',
        ecmaVersion: 11
    },
    env: {
        browser: true
    },
    extends: ['eslint:recommended'],
    rules: {
        
    }
}
```

如果需要使用流行风格指南 `standard`，需要安装以下包：

```sh
npm install --save-dev "eslint-config-standard eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-plugin-standard
```

然后修改 `.eslintrc.js` 的 `extends` 选项：

```js
module.exports = {
    // ...
    extends: ['standard'],
}
```

4. 创建 `.eslintignore` 文件，部分路径和文件不启用 eslint 检查：

```sh
/dist/
/*.js
```

> 根目录下的配置相关的文件夹也应添加到该文件中

## 检查 Vue 文件及语法