# Codewars刷题升级1 - 【正则生成电话号】- 6sku - Creat Phone Number
## 问题描述
Write a function that accepts an array of 10 integers (between 0 and 9), that returns a string of those numbers in the form of a phone number.

译：编写一个函数，该函数接受一个由10个整数组成的数组(在0到9之间)，并以电话号码的形式返回这些数字的字符串。

例:

```js
createPhoneNumber([1, 2, 3, 4, 5, 6, 7, 8, 9, 0]) // => returns "(123) 456-7890"
```

## 问题拆解
1. 将数组转化为字符串
2. 正则替换`(***) ***-****`

## 解题
1. `String.prototype.replace()`方法中的第二个参数可以使用 `$n` 来插入匹配的第 `n` 个括号匹配的字符串。**注意：**

    - 需要第一个参数为[RegExp](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)对象。
    - `n` 为非负整数，取值范围为 `[1, 100)`

```js
function createPhoneNumber(numbers){
  return numbers.join('').replace(/(\d{3})(\d{3})(\d{4})/,'($1) $2-$3')
}
```

2. 测试
```js 
console.log(createPhoneNumber([1, 2, 3, 4, 5, 6, 7, 8, 9, 0])); // "(123) 456-7890"
```

## Codewars精选解
成功提交自己的答案后，可以浏览别人的解，有最佳实践和最聪明解（clever）。

- 这个是最佳实践票数最高的：
```js
function createPhoneNumber(numbers){
  numbers = numbers.join('');
  return '(' + numbers.substring(0, 3) + ') ' 
      + numbers.substring(3, 6) 
      + '-' 
      + numbers.substring(6);
}
```
代码简单，效率高，最佳实践，没毛病。

- 再看看最聪明解：

```js
function createPhoneNumber(numbers){
  return numbers.join('').replace(/(...)(...)(.*)/, '($1) $2-$3');
}
```

我的解跟这个解类似，但此解兼容性更高一些。

- 最后看看WTF解：

```js
function createPhoneNumber(numbers){
  var n = numbers;
  return '('+n[0]+n[1]+n[2]+') '+n[3]+n[4]+n[5]+'-'+n[6]+n[7]+n[8]+n[9];
}
```

这。。。是不是太low了?

来看看网友评论：
```
Some might say this isn't the most elegant to look at, but in my benchmark I found this solution to be the fastest by far. More than twice as fast as the closest competitor.
```
`虽然代码不是很优雅，但经过测试是目前最快的` 嗯，你说的很有道理。
```
Seems to run the fastest. Sometimes the simpler solution is the best.
```

好吧，代码不是很优雅，不过有网友说这是最快的代码。

但很多时候，语义、可读性、编码体验更重要，难道因为`for`循环效率更高，我们就不去使用`map`、`forEach`了吗？不要陷入过分追求性能的误区。


## 本题相关知识
1. [String.prototype.replace()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
2. [String.prototype.substring()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/substring)
3. [正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

[欢迎star](https://github.com/hiblacker/codewars-daily)

(完)