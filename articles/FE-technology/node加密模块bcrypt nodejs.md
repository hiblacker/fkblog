
Node版加密模块，原生JS实现。
<!-- more -->

# 基本使用 :

同步：
```js
var hash = bcrypt.hashSync("bacon");
 
bcrypt.compareSync("bacon", hash); // true
bcrypt.compareSync("veggies", hash); // false
```
异步：
```js
bcrypt.hash("bacon", null, null, function(err, hash) {
    // Store hash in your password DB.
});
 
// Load hash from your password DB.
bcrypt.compare("bacon", hash, function(err, res) {
    // res == true
});
bcrypt.compare("veggies", hash, function(err, res) {
    // res = false
});
```
在上面的例子中，加密盐自动生成并添加到散列中。虽然你可以使用自定义salt，但没必要，因为它总是会被加入到最终的散列中并可以重新取回。

# API
找了半天没有找到中文版，为了加深记忆，方便以后翻阅，自己翻译过来吧~英语很渣，如有错误，请指出~，[原文地址](https://www.npmjs.com/package/bcrypt-nodejs)
***
` genSaltSync ( rounds ) `
- `rounds`： 可选，哈希次数，默认为10。
***
` genSalt ( rounds, callback ( error, result )) `
- `rounds`： 可选，哈希次数，默认为10。
- `callback`： **必需**，salt 生成时执行的回调函数。
    - `error`： 返回各种错误。
    - `result`： 返回生成的salt。
***
`hashSync ( data, salt )`
- `data`： **必需**，要加密的数据。
- `salt`： **必需**，加密时使用的salt。
***
`hash ( data, salt, progress, callback ( error, result ))`
- `data`： **必需**，要加密的数据。
- `salt`： **必需**，哈希密码的 `salt`。
- `progress `：  进行哈希计算时执行的。。
- `callback`：**必需**，`data` 加密完成后执行的。。
    - `error`：返回各种错误。
    - `result`： 返回加密形式。
***
`compareSync ( data, encrypted )`
- `data`： **必需**，用来比较的数据。
- `encrypted`： **必需**，用来被比较的数据。
***
`compare ( data, encrypted, cb )`
- `data`： **必需**，用来比较的数据。
- `encrypted`： **必需**，用来被比较的数据。
- `callback`：**必需**，`data` 比较完成后执行的回调函数。
    - `error`：返回各种错误。
    - `result`： 返回加密形式是否匹配[ true | false ]。
***
`getRounds ( encrypted )`
返回加密散列的哈希次数。
`encrypted`：**必需**， 要提取哈希次数的加密散列。
***