## 问题描述
The drawing shows 6 squares the sides of which have a length of 1, 1, 2, 3, 5, 8. It's easy to see that the sum of the perimeters of these squares is : `4 * (1 + 1 + 2 + 3 + 5 + 8) = 4 * 20 = 80`

Could you give the sum of the perimeters of all the squares in a rectangle when there are n + 1 squares disposed in the same manner as in the drawing:

![](http://qiniu.lanjinrong.com/2e5779b126aa4e91b93fbcded330c185)

#Hint: See Fibonacci sequence

#Ref: http://oeis.org/A000045

The function perimeter has for parameter n where n + 1 is the number of squares (they are numbered from 0 to n) and returns the total perimeter of all the squares.
```
perimeter(5)  //输出：80
perimeter(7)  //输出：216
perimeter(20) //输出：114624
perimeter(30) //输出：14098308
```

## 解题思路
1. 生成长度为 `n` 的斐波纳契数组 `[1, 1, 2, 3, 5, 8] `
2. 求周长 `4 * (1 + 1 + 2 + 3 + 5 + 8)`

## 解题
题中给出 `n` 的范围是 `[0,+∞)` 所以这里不处理 `n` 的入参，codewars的风格就是这样，专心解题就OK，很多边缘情况不需要考虑。
1. 生成斐波纳契数组
```js
function generateFib(n) {
    if (n < 2) return Array(n + 1).fill(1)
    let arr = [1, 1]
    while (--n) {
        arr.push(arr[arr.length - 1] + arr[arr.length - 2])
    }
    return arr
}
```

2. 求周长

```js
function perimeter(n){
    return generateFib(n).reduce((sum,cur) => sum + cur) * 4
}
```

3. 一行代码版
```js
/**
 * 生成斐波那契数列

 * 第一次 reduce ：将数组最后两项的和放入数组最后
 * 第二次 reduce ：求周长
 * 
 */
function perimeterInOne(n){
    return [...Array(n+1).keys()]
            .reduce(arr => [...arr, (arr[arr.length - 1] || 1) + (arr[arr.length - 2] || 0)], [])
            .reduce((sum, cur) => sum + cur) * 4
}
```

4. 测试
```js 
console.log(perimeter(0)); // 4
console.log(perimeter(1)); // 8
console.log(perimeter(5)); // 80
console.log(perimeter(20)); // 114624
console.log(perimeterInOne(0)); // 4
console.log(perimeterInOne(1)); // 8
console.log(perimeterInOne(5)); // 80
console.log(perimeterInOne(20)); // 114624
```

## Codewars精选解
成功提交自己的答案后，可以浏览别人的解，有最佳实践和最骚解（clever）。
我选了一个有意思的：
```js
function perimeter(n){
    const SQRT5 = Math.sqrt(5); 
    const PHI = (SQRT5 + 1) / 2;
    const fib = n => Math.round(PHI**n / SQRT5);
    const perimeter = n => 4 * fib(n + 3) - 4;
}
```
看到的第一眼我是崩溃的，WTF？平复了一下心情后我想了一下，这应该是用到数学知识了。果然数学好就可以为所欲为。

查了一下，果然，根据通项公式得到前n项和的公式，然后乘以4就是本题的解了。

感兴趣可以自行[谷歌](https://www.google.com/?q=斐波那契数列) 或者 [百度](https://www.baidu.com/s?wd=斐波那契数列)斐波那契数列。

## 本题相关知识
1. [Array.prototype.fill()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/fill)
2. [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
3. [`=>` 箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
4. [`...` 拓展运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

[欢迎star](https://github.com/hiblacker/codewars-daily)

(完)