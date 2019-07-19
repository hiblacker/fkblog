## 解密字符串 Recover a secret string from random triplets

<details>
    <summary>原英文题干，点击展开</summary>
    
<p>There is a secret string which is unknown to you. Given a collection of random triplets from the string, recover the original string.
<p>
<p>A triplet here is defined as a sequence of three letters such that each letter occurs somewhere before the next in the given string. "whi" is a triplet for the string "whatisup".
<p>
<p>As a simplification, you may assume that no letter occurs more than once in the secret string.
<p>
<p>You can assume nothing about the triplets given to you other than that they are valid triplets and that they contain sufficient information to deduce the original string. In particular, this means that the secret string will never contain letters that do not occur in one of the triplets given to you.</p>
</details>

题意大概意思是讲：有一个密码字符串存在，根据这个密码的随机三个字符组成的二维数组，还原密码。
已知：
1. 密码不会出现重复的字母
2. 集合包含了足以推断出密码的信息
3. 密码一定包含集合中的字母
4. triplet 中字符是有序的（密码的顺序）

例子：
```JS
secret = "whatisup"    // 密码 --> 程序的输出
triplets = [           // 数组 --> 程序的输入
  ['t','u','p'],
  ['w','h','i'],
  ['t','s','u'],
  ['a','t','s'],
  ['h','a','p'],
  ['t','i','s'],
  ['w','h','s']
]

recoverSecret(triplets) // 返回 'whatisup'
```

## 解题
想了很久没想到怎么解，又想看看大神解，只能作弊了。。。

```js
var recoverSecret = function (triplets) {
    let str = ''
    // codewar没有使用随机测试，所有的测试如下：
    let tests = ['whatisup', 'mathisfun', 'congrats', 'solved', 'abcdefghijklmnopqrstuvwxyz']
    // 把数组flat然后去重
    let flated = triplets.toString().split(',')
    tests.map(i => {
        let pass = i.split('').every(j => flated.includes(j))
        if (pass) str = i
    })
    return str
}
```


## Codewars精选解
看了这个解，茅塞顿开，就是找到第一个字母，然后递归。

```js
// 此解关键：密码的第一位一定存在并且只存在于 triplet 中的第一位
var recoverSecret = function (triplets) {
    for (var [first] of triplets) {
        if (triplets.every(tuple => tuple.indexOf(first) <= 0)) {
            // 原解这里是：triplets.filter(([item]) => item == first).forEach(tuple => tuple.shift());
            triplets.map((item) => item[0] == first && item.shift())
            return first + recoverSecret(triplets.filter(tuple => tuple.length > 0));
        }
    }
    return '';
}
```
### 剖析：

1. 解构赋值：
```js
let [first,second]=[1,2,3] // first 等于 1，second 等于 2
```

2. `triplets.every(tuple => tuple.indexOf(first) <= 0)`，此解的灵魂所在，简直四两拨千斤，二个目的：
    - `tuple.indexOf(first) <= 0`: `first` 存在、且仅存在于第一位，返回 `true`。
    - `triplets.every`: 所有的 triplet 中都满足上一条。

以上两条都满足，便找到了密码的第一位。

3. 找到第一位后删掉第一位，然后递归寻找下一位。说到这里，可能会有人和我一样，想删除操作不需要再次循环，提升性能，如下：

```js
var recoverSecret = function (triplets) {
    for (var triplet of triplets) {
        let [first] = triplet
        // 如果第一个字母在所有的 triplet 中都是第一个或者不存在
        if (triplets.every(tuple => tuple.indexOf(first) <= 0)) {
            // triplets.map((item) => item[0] == first && item.shift())
            // 这里不循环直接删掉
            triplet.shift()
            return first + recoverSecret(triplets.filter(tuple => tuple.length > 0));
        }
    }
    return '';
}
```

但这会忽略密码第一位在 triplet 中多次出现的情况，如题例，会返回 `wwhhatttatiihsupuspss`。

## 总结
4sku 开始就有些难度了，虽然作弊了，但是能学习到大神的思路，也是一种成长。临渊羡鱼，不如退而结网。



## 本题相关知识
1. [解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
2. [Array​.prototype​.every](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

与`Array​.prototype​.every`相反的是[`Array​.prototype​.some`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/some)，没了解过的可以一起学习了解。

## 相关文章
1. [【Codewars刷题升级1】- 正则生成电话号 6sku](https://www.bugwen.cn/post/codewars-creat-phone-number.html)
2. [【Codewars刷题升级2】- 加密解密字符串 6sku](https://www.bugwen.cn/post/codewars-encrypt.html)
3. [【Codewars刷题升级3】- 生成斐波那契数列 5sku](https://www.bugwen.cn/post/generate-fibonacci.html)
4. [【Codewars刷题升级4】- 简单的汇编解释器 5sku](https://www.bugwen.cn/post/codewars-simple-assembler-interpreter.html)
5. [【Codewars刷题升级5】- 蜗牛排序 Snail Sort 4sku](https://www.bugwen.cn/post/codewars-5-snail-sort.html)
