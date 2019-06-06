在很久之前我写过几个 Node.js开发命令行小工具的教程：看段子的、翻译的。但是只是介绍了如何实现功能，并没有详细介绍一个健壮 npm 包的开发。如果你对 Node.js 命令行工具开发还没什么了解，可以看看 [Node命令行工具开发【看段子】](https://mp.weixin.qq.com/s/ri7P0sJ_wh6bFoYwNeKPNQ)

今天我们就来开发并上线一个命令行翻译工具。

GitHub：https://github.com/liuxing/translator-cli

关于如何开发并发一个 npm，这个问题很简单，可以参考 [如何开发一个npm包并发布到中央仓库](https://bugwen.cn/post/how-to-develop-npm-package.html)。但是我们这节的目标是开发一个健壮牛逼的包，所以本节功能实现不是重点而是一些工具的使用以及开发规范。

## 初始化项目
我们先来新建一个项目
```bash
# 新建目录
$ mkdir translator-cli && cd translator-cli
# 然后执行npm init生成package.json文件
$ npm init 
```
虽然之前讲过如何开发命令行小工具，这儿还是再多说两句

新建一个`hi.js`
```bash
#! /usr/bin/env node
console.log("朋友你们好")
```
执行 `node hi.js` 我们可以看到终端输出“ 朋友你们好” ，现在在 `package.json` 文件中添加
```bash
"bin": {
   "hi": "hi.js"
}
```
执行 `npm link` ，现在我们不再需要 `node hi.js` 来执行了。只需要运行 `hi` 即可。当然，现在也可以将你的这个命令行工具发布到 npm，你可以给他起个名字就叫 hi，但是我猜这个包名已经有人用了。

前面我们说了本节的目标是发布一个健壮的包，这个未免也太简陋了，Node 社区也不乏核心代码只有几行的包，虽然他们的核心功能代码只有几行，但却有个大量的开发配置文件、测试代码。

## 开发命令行翻译工具
继续来开发我们的这个命令行翻译工具，它功能是获取命令行用户的输入然后翻译。重点就在于获取参数以及网络请求。

合理的目录结构

一个健壮的项目，应该要有个层次清晰的目录结构
```
|--translator-cli
  |-- bin/ 命令脚本文件
  |-- lib/ 库文件 一些功能代码
  |-- test/ 测试
  |-- package.json
  |-- ··· more
```
这只是我这个命令行项目的结构。对于其他项目也是一样，合理的目录结构百益而无一害，我们还可以有 `docs` 用来放文档 `script` 用来放构建脚本……

## 代码风格与代码检查

### 使用 editorconfig
在项目中我们使用 `.editorconfig` 文件 统一代码风格 http://editorconfig.org ，该文件用来定义项目的编码规范，编辑器的行为会与 `.editorconfig` 文件中定义的一致，并且其优先级比编辑器自身的设置要高，这在多人合作开发项目时十分有用而且必要。
```conf
# editorconfig.org
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false
```

### 使用ESLInt
ESLint http://eslint.cn 是一个插件化的 Javascript 代码检测工具，它可以用于检查常见的 JavaScript 代码错误，也可以进行代码风格检查，这样我们就可以根据自己的喜好指定一套 ESLint 配置，然后应用到所编写的项目上，从而实现辅助编码规范的执行，有效控制项目代码的质量。

在开始使用 ESLint 之前，我们需要通过 NPM 来安装它：
```bash
$ npm install -g eslint
# 我们也可以将它安装到项目开发依赖中
$ npm install --save-dev eslint
```
接下来就可以使用 `eslint *.js` 来检查代码。我们还可以与 Git hooks 配合，在提交时自动检查。

## 核心代码编写

经过前面一系列准备后，终于我们开始编写代码了。这儿用到了 TJ 的 commander 来编写命令行接口、使用 superagent 去请求 API 接口以及使用 chalk 美化输出

由于现在功能还很单一，代码也很简单，这儿只贴核心代码了，完整代码查看GitHub https://github.com/liuxing/translator-cli

使用 commander 来处理命令行参数
```JS
const program = require('commander')
const pkg = require('../package.json')

// 使用commander
program
  .version(pkg.version)

program
  .command('query <words>')
  .description('Query words')
  .action(translator.query)

program.parse(process.argv)

if (process.argv.length === 2) {
  program.outputHelp()
}
```

使用 superagent 去请求接口

```JS
const request = require('superagent')
const api = require('./api.json')

exports.query = function (query) {
  request
    .get(api['youdao'])
    .query({ q: query })
    .then(function (res) {
      const data = res.body
      parseYoudao(data)
    })
    .catch(function (err) {
      console.error(err)
    })
}
```

处理返回的内容，并美化输出

```JS
const chalk = require('chalk')
const log = console.log

function parseYoudao (data) {
  let header = ''
  header += data.query
  if (data.basic && data.basic.phonetic) {
    header += chalk.magenta('  [ ' + data.basic.phonetic + ' ]')
  }
  log(header + chalk.gray('    ~ fanyi.youdao.com'))
  // explains
  if (data.basic && data.basic.explains) {
    log()
    data.basic.explains.forEach((item) => {
      log(chalk.gray('- ') + chalk.green(item))
    })
  }
  // sentence
  if (data.web && data.web.length) {
    log()
    data.web.forEach((item, i) => {
      log(chalk.gray(i + 1 + '. ') + item.key)
      log('   ' + chalk.cyan(item.value.join(',')))
    })
  }
}
```

完整代码查看GitHub https://github.com/liuxing/translator-cli

## 编写测试

一个健壮的包，我们一定要写测试用例，这样能让我们的代码跑的舒心，使用者用的舒心。不写测试的项目都不是好项目，node 中最为流行的组合是 mocha 和 chai，当然了ava 也是一个强大的后起之秀

下载安装 mocha 和 chai

```BASH
$ npm install mocha chai --save-dev
```

使用它们，比如说来测试一个 `add function`

```js
//add.js
module.exports =  function add(a,b) {
    return a + b
}
```

编写测试用例

```JS
const chai = require('chai'); 
const should = chai.should()
const add = require('./add')

describe("add func test",() => {
    it('2 add 2 should equal 4',() => {
      add(2,2).should.equal(4)
    })
})
```

关于测试，更多请自行搜索学习

## 其他重要的事

除此之外，一个项目应该添加个 `README.md` 来介绍这个包的功能，开源项目还应该有个 `LICENSE` ，来声明我们的开源协议。关于如何写好一个 README，我觉得至少应该写清楚名字、功能描述、使用指引、开原协议等。

## 正式发布这个包

注意：使用 npm 的源不要使用其他如：taobao

先去 npm 注册个账号，然后在命令行使用

```BASH
$ npm adduser # 根据提示输入用户名密码即可
```

现在在来一个命令你的包就可以成功发布了，

```bash
$ npm publish
```

现在你就可以像下载安装其他包一样使用这个你自己开发的工具了

```bash
$ npm install translator-cli
```

## 关于包的更新

更新 npm 包也是使用 `npm publish` 命令发布，不过必须更改 npm 包的版本号，即 `package.json` 的 `version` 字段，否则会报错，同时我们应该遵 Semver (语义化版本号) 规范，npm 提供了 `npm version` 给我们升级版本

```bash
# 升级补丁版本号
$ npm version patch
# 升级小版本号
$ npm version minor
# 升级大版本号
$ npm version major
```

## 总结

到这儿，本文就完了，你肯定还意犹未尽，文中很多东西都还需要自己去了解学习。本文标题是如何开发发布一个npm包，实际是一个npm包为例，讲解如何开发一个健壮的项目，当然还有很多东西没涉及到如持续集成，代码覆盖率……

[原文链接](https://zhuanlan.zhihu.com/p/37316872)