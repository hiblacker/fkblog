## 问题描述
Codewars 中难度等级由高到低为：8sku -> 1sku，1sku 最难。本题难度 6sku。

> For building the encrypted string:
> Take every 2nd char from the string, then the other chars, that are not every 2nd char, and concat them as new String.
> Do this n times!

译：生成加密字符串：从字符串中取出第 2n 个字符，把它们和余下的字符串拼成新字符串。重复 n 次。

例:

```js
encrypt("This is a test!", 1) // 输出："hsi  etTi sats!"
encrypt("This is a test!", 2) // 输出："s eT ashi tist!"，其实就是 "hsi  etTi sats!" 经过一次变换得到的。
```

> For both methods:
> If the input-string is `null` or empty return exactly this value!
> If `n` is <= 0 then return the input text.

译：对于两个方法：

1. 如果传入字符串为 `null` 或空，则直接返回这个值。
2. 如果 `n` <= 0 ，则直接返回字符串。


## 解题思路

加密就是根据传入的 `n` 相隔 `f(n)` 依次取出字符组成新字符串。

解密就是将新字符串相隔 `f(n)` 放回原字符串。

1. 加密

    - 将字符串转换成数组方便处理
    - 循环取出当前数组第 `x` 个字符放入新数组，`x` 根据题意取 `2` 的 `n` 次方再乘以 `k`，`k` 为第 `k` 次循环
    - 合并字符串

2. 解密
    - 新建一个等于字符串长度的数组（原字符串数组）
    - 循环新数组，依次将字符放回原字符串的 `f(n) * k` 处
    - 返回原字符串

## 解题
看之前最好自己先想一下，如果是你，如何解决，不然你很快就会忘记，知识还是我自己的。由于我没有接触过此类问题，想了1个多小时才有了下面笨拙生硬的解决方案。可跳过我的解，直接看精选解。
```js
function encrypt(text, n) {
    return n <= 0 || !text
        ? text
        : text
              .split('')
              .map(
                  (i, k) =>
                      text
                          .split('')
                          .splice(
                              (Math.pow(2, n) * (k + 1) - 1) %
                                  (text.length % 2 == 0 ? text.length + 1 : text.length),
                              1,
                          )[0],
              )
              .join('');
}

function decrypt(encryptedText, n) {
    if (!encryptedText || n <= 0) return encryptedText;
    let arr = new Array(encryptedText.length);
    encryptedText
        .split('')
        .map(
            (i, k) =>
                (arr[
                    (Math.pow(2, n) * (k + 1) - 1) %
                        (encryptedText.length % 2 == 0
                            ? encryptedText.length + 1
                            : encryptedText.length)
                ] = i),
        );
    return arr.join('');
}
```

## Codewars 精选解

成功提交自己的答案后，可以浏览大神的解。

-   最佳实践：

```js
function encrypt(text, n) {
    if (!text || n <= 0) return text;
    while (n--) {
        let ans = '';
        for (let i = 1; i < text.length; i += 2) {
            ans += text[i];
        }
        for (let i = 0; i < text.length; i += 2) {
            ans += text[i];
        }
        text = ans;
    }
    return text;
}

function decrypt(encryptedText, n) {
    if (!encryptedText || n <= 0) return encryptedText;
    const ans = new Array(encryptedText.length);
    while (n--) {
        let j = 0;
        for (let i = 1; i < ans.length; i += 2) {
            ans[i] = encryptedText[j++];
        }
        for (let i = 0; i < ans.length; i += 2) {
            ans[i] = encryptedText[j++];
        }
        encryptedText = ans.join('');
    }
    return encryptedText;
}
```

-   再看看最聪明解：

```js
function encrypt(text, n) {
    for (let i = 0; i < n; i++) {
        text = text && text.replace(/.(.|$)/g, '$1') + text.replace(/(.)./g, '$1');
    }
    return text;
}

function decrypt(text, n) {
    let l = text && (text.length / 2) | 0;
    for (let i = 0; i < n; i++) {
        text = text.slice(l).replace(/./g, (_, i) => _ + (i < l ? text[i] : ''));
    }
    return text;
}
```

理解此解需要一定的正则知识。
### 剖析 `/.(.|$)/g` ：

`.` 匹配任意单个字符，`$` 匹配字符串的结束，括号被称为捕获括号，会匹配并记住括号中字符串，`replace()` 方法中 `$n` 对应匹配的第 n 个字符串。如：

```js
'bar foo'.replace( /(...) (...)/, '$2 $1' )
// 输出："foo bar"
```

```js
'12345'.replace(/.(.)/g, '$1')
// 输出：245

'12345'.replace(/.(.|$)/g, '$1')
// 输出：24

'12345'.replace(/.(.|$)/g, (match,a,b)=>{
	console.log(match,a1,a2)
	return a
})
// 输出：
// 12 2 0
// 34 4 2
// "245"
```

因此 `text.replace(/.(.|$)/g, '$1')` 会返回源字符串取出第 2n 个字符后组成的新字符串，对应的余下的字符串就是 `text.replace(/(.)./g, '$1')`。

### 剖析解密函数 `decrypt()`
```js
function decrypt(text, n) {
    let l = text && (text.length / 2) | 0;
    for (let i = 0; i < n; i++) {
        text = text.slice(l).replace(/./g, (_, i) => _ + (i < l ? text[i] : ''));
    }
    return text;
}
```
先分析最简单的情况，`123456`，一次加密的结果是 `246135`。想象一下，钢琴按键, `123456` 对应 `白黑白黑白黑` ，一次加密就是拿出 `黑`，拼到一起，放到桌子上，再将余下的 `白`，拼到一起，放到 `黑` 的后边，变成 `黑黑黑白白白`，加密完成。

然后解密，分两步还原钢琴，第一步，黑白分组（`text.slice(l)`）；第二步，白在前，黑在后，交错相放（`_ + (i < l ? text[i] : '')`），还原完成。

## 总结
看到这道题的第一眼，我是懵逼的，一点头绪没有，硬着头皮想了一个多小时，勉强解出。再到学习别人的解，总结成文，耗时3天，约10小时，还好皇天不负有心人，被我弄清楚了。

很多知识都是这样，看到的第一眼是完全不能理解的，但要相信滴水穿石，总有一天，你会豁然开朗，个中滋味，无法言语。

## 相关知识

1. [String.prototype.replace()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
2. [正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

[欢迎交流](https://github.com/hiblacker/codewars-daily)
