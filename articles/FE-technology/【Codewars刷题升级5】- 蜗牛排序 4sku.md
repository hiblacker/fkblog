## 蜗牛排序 Snail Sort

<details>
    <summary>原英文题干，点击展开</summary>
    
<div class="markdown" id="description"><h2 id="snail-sort">Snail Sort</h2>
<p>Given an <code>n x n</code> array, return the array elements arranged from outermost elements to the middle element, traveling clockwise.</p>
<pre><code>array = [[1,2,3],
         [4,5,6],
         [7,8,9]]
snail(array) #=&gt; [1,2,3,6,9,8,7,4,5]</code></pre><p>For better understanding, please follow the numbers of the next array consecutively:</p>
<pre><code>array = [[1,2,3],
         [8,9,4],
         [7,6,5]]
snail(array) #=&gt; [1,2,3,4,5,6,7,8,9]</code></pre><p>This image will illustrate things more clearly:</p>
<img src="http://www.haan.lu/files/2513/8347/2456/snail.png">

<p>NOTE: The idea is not sort the elements from the lowest value to the highest; the idea is to traverse the 2-d array in a clockwise snailshell pattern.</p>
<p>NOTE 2: The 0x0 (empty matrix) is represented as <code>[[]]</code></p>
</div>
</details>

意译：

给定一个`n x n`二维数组，返回按顺时针方向从最外层元素排列到中间元素的数组。

```js
let array = [
        [1,2,3],
        [4,5,6],
        [7,8,9]]
snail(array) => [1,2,3,6,9,8,7,4,5]
let array2 = [
        [1,2,3,1],
        [4,5,6,4],
        [7,8,9,7],
        [7,8,9,7]]
snail(array2) => [1,2,3,1,4,7,7,9,8,7,7,4,5,6,9,8]
```

如图：

![](http://qiniu.lanjinrong.com/d5b9a71a9b418d71a3539de5713a6220)

NOTE: 其思想不是将元素从最低值排序到最高值;其思想是按照顺时针蜗牛壳模式遍历二维数组.

NOTE 2: `0x0`(空矩阵)表示为`[[]]`

## 问题拆解

1. 顺时针取最外层元素，并删除
2. 递归至最里层

## 解题

我依然笨拙的解题思路：按照题意，先取最外层数据，上 → 右 → 下 → 左，四个 `for` 循环，然后递归。

```js
let snail = function(array) {
    let n = array[0].length;
    if (n === 0) return [];
    let copyArr = array;
    let newArr = [];
    circle(copyArr);

    function circle(copyArr) {
        let n = copyArr[0].length;
        if (copyArr[0].length === 1) {
            newArr.push(copyArr[0][0]);
            return;
        }
        // 依次取上侧数据
        for (let i = 0; i < n; i++) {
            newArr.push(copyArr[0][i]);
        }
        // 依次取右侧数据
        for (let i = 1; i < n; i++) {
            newArr.push(copyArr[i][n - 1]);
        }
        // 依次取下侧数据
        for (let i = n - 2; i > 0; i--) {
            newArr.push(copyArr[n - 1][i]);
        }
        // 依次取做侧数据
        for (let i = n - 1; i > 0; i--) {
            newArr.push(copyArr[i][0]);
        }
        // 删掉最外层数据
        // 删除第n行
        copyArr.splice(n - 1, 1);
        // 删除第0行
        copyArr.splice(0, 1);
        // 删除两边
        copyArr = copyArr.map(i => {
            i.pop();
            i.shift();
            return i;
        });
        // 删除空数组
        copyArr.map((i, k) => (i.length === 0 ? copyArr.splice(k, 1) : ''));
        // 保证[[]]结构
        if (!copyArr[0]) copyArr[0] = [];
        if (copyArr[0].length > 0) circle(copyArr);
    }
    return newArr;
};
```

## Codewars 精选解

此解巧妙的运用数组的`shift()`和`pop()`方法的两个特性：

1. `shift()`和`pop()`分别删除数组第一项、最后一项，并返回删除的元素。
2. 改变原数组。

用在此题可以说是四两拨千斤，反观自己的解，简直笨重！

```js
snail = function(array) {
    var result;
    while (array.length) {
        // Steal the first row.
        result = result ? result.concat(array.shift()) : array.shift();
        // Steal the right items.
        for (var i = 0; i < array.length; i++) result.push(array[i].pop());
        // Steal the bottom row.
        result = result.concat((array.pop() || []).reverse());
        // Steal the left items.
        for (var i = array.length - 1; i >= 0; i--) result.push(array[i].shift());
    }
    return result;
};
```

## 本题相关知识

1. [Array.prototype.shift](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)
2. [Array.prototype.pop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)
3. [Array.prototype.concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)
4. [Array.prototype.reverse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)

## 相关文章
1. [【Codewars刷题升级1】- 正则生成电话号 6sku](https://www.bugwen.cn/post/codewars-creat-phone-number.html)
2. [【Codewars刷题升级2】- 加密解密字符串 6sku](https://www.bugwen.cn/post/codewars-encrypt.html)
3. [【Codewars刷题升级3】- 生成斐波那契数列 5sku](https://www.bugwen.cn/post/generate-fibonacci.html)
4. [【Codewars刷题升级4】- 简单的汇编解释器 5sku](https://www.bugwen.cn/post/codewars-simple-assembler-interpreter.html)
