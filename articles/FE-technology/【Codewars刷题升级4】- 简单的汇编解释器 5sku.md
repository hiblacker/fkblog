## 简单的汇编解释器 Simple assembler interpreter

<details>
    <summary>原英文题干，点击展开</summary>
    
<p>This is the first part of this kata series. (后面还有 part2)

<p>We want to create a simple interpreter of assembler which will support the following instructions:

<p>-   `mov x y` - copies `y` (either a constant value or the content of a register) into register `x`
<p>-   `inc x` - increases the content of register `x` by one
<p>-   `dec x` - decreases the content of register `x` by one
<p>-   `jnz x y` - jumps to an instruction `y` steps away (positive means forward, negative means backward), but only if `x` (a constant or a register) is not zero

<p>Register names are alphabetical (letters only). Constants are always integers (positive or negative).

<p>Note: the `jnz` instruction moves relative to itself. For example, an offset of `-1` would continue at the previous instruction, while an offset of `2` would skip over the next instruction.

<p>The function will take an input list with the sequence of the program instructions and will return a dictionary with the contents of the registers.

<p>Also, every `inc`/`dec`/`jnz` on a register will always be followed by a `mov` on the register first, so you don't need to worry about uninitialized registers.</p>

<h3> Example</h3>

<img src='https://bugwen.cn/static/upload/20190716/upload_5b580a8d906c227e788840dfd1edf494.png'>

<p>The above code will:

<p>1. set register `a` to `5`,
<p>2. increase its value by `1`,
<p>3. decrease its value by `2`,
<p>4. then decrease its value until it is zero (`jnz a -1` jumps to the previous instruction if `a` is not zero)
<p>5. and then increase its value by `1`, leaving register `a` at `1`
<p>
<p>So, the function should return

<code>
{'a': 1}
</code>
<p>
<p>This kata is based on the [Advent of Code 2016 - day 12](https://adventofcode.com/2016/day/12)</p>

</details>

意译：

创建一个简单的汇编程序解释器，它将支持以下指令：

-   `mov x y`- 将 `y`（可以是寄存器、常数值）复制到寄存器 `x` 中
-   `inc x`- 将寄存器 `x` 加一
-   `dec x`- 将寄存器 `x` 减一
-   `jnz x y`- 跳转到指令 `y` 步骤（正值表示向后跳转，负值表示向后跳转），但仅限于寄存器 `x` 的值不为零

寄存器名称是都是字母（仅字母）。常量总是整数（正数或负数）。

注意：`jnz` 指令相对于自身移动。例如，偏移量 `-1` 将在前一条指令处继续，而偏移量 `2` 将跳过下一条指令。

函数的参数为带有程序指令序列的输入列表，返回包含寄存器键值的 Json。

此外，寄存器上的每个 `inc`/ `dec` / `jnz` 指令总是在 `mov` 之后，因此不必担心未初始化的寄存器。

### 例子

```js
simple_assembler(['mov a 5','inc a','dec a','dec a','jnz a -1','inc a'])

''' visualized:
mov a 5
inc a
dec a
dec a
jnz a -1
inc a
''''
```

上面的代码将：

1. 注册寄存器 `a` 值为 `5`，
2. `a` 加 `1`，
3. `a` 减 `1`，
4. `a` 循环递减，直到值为零（`jnz a -1` 如果 `a` 不为零则跳转到前一条指令）
5. `a` 加 `1`，

因此函数最后返回：

```js
{'a': 1}
```

此 kata 基于[Advent of Code 2016 - day 12](https://adventofcode.com/2016/day/12)

## 解题思路

1. 生成长度为 `n` 的斐波纳契数组 `[1, 1, 2, 3, 5, 8]`
2. 求周长 `4 * (1 + 1 + 2 + 3 + 5 + 8)`

## 解题

解析命令，根据命令执行对应的操作就可以了，不好做的是 `jnz` 跳转命令。我的思路是存在一个指针 `pointer` 来指向当前应该执行的操作，当遇到跳转命令时，根据自身位置和命令参数修改指针，跳出当前循环，指针指向目标命令，然后执行命令。

```js
function simple_assembler(program) {
    let context = {},
        pointer = 0,
        getInstruction = instruction => instruction.split(' ');
    operate = {
        // 兼容 mov a b 这种将 b 赋值给 a 的情况
        mov: (key, value) => (context[key] = context[value] || +value),
        inc: key => ++context[key],
        dec: key => --context[key],
    };
    while (pointer < program.length) {
        let instruction = program[pointer],
            inst = getInstruction(instruction);
        if (inst[0] !== 'jnz') {
            operate[inst[0]](...inst.slice(1));
        } else if (context[inst[1]] !== 0) {
            let location = pointer + +inst[2];
            pointer = location;
            continue;
        }
        pointer++;
    }
    return context;
}
```

简单测试，通过，但是无法通过随机测试，报错：

```
Execution Timed Out (12000 ms)
```

哦买噶？超时？测试用例有 bug 吧？这是我的第一反应，由于没有提交答案，无法查看别人的解，于是我去讨论区看了一下，果然，有人跟我有一样的想法，也有人提了 `issue` 但得到的回复是测试用例没问题，请优化代码云云，好吧，想想也对，这么成熟的网站不会犯这种低级错误。

于是我又把精力放在代码上。将测试用例打印出来，发现其中一个测试用例 `while` 会循环上十万次，那么就从循环开始。

我注意到每次循环都会执行 `split` 操作，完全可以缓存起来，直接读取就好了：

```js
function simple_assembler(program) {
    let context = {},
        pointer = 0,
        // getInstruction = instruction => instruction.split(' ')
        operate = {
            // 兼容 mov a b 这种将 b 赋值给 a 的情况 +value 将字符串转换为 number
            mov: (key, value) => (context[key] = context[value] || +value),
            inc: key => ++context[key],
            dec: key => --context[key],
        };
    program = program.map(i => i.split(' '));
    while (pointer < program.length) {
        let inst = program[pointer];
        if (inst[0] !== 'jnz') {
            operate[inst[0]](...inst.slice(1));
        } else if (context[inst[1]] !== 0) {
            let location = pointer + +inst[2];
            pointer = location;
            continue;
        }
        pointer++;
    }
    return context;
}
```

简单测试，通过。随机测试，1s，2s，3s... `Execution Timed Out (12000 ms)` ...

WTF？还超时？ 怎么可以这样... 不过我喜欢！ 继续折腾 ~

`mov` 方法这里有点取巧，通过逻辑或来赋值，效率不如三元操作符，由于不需要考虑空字符串等边界情况，因此这里用 `isNaN` 来判断数字值，修改如下：

```js
// (key, value) => (context[key] = context[value] || +value)
(key, value) => (context[key] = isNaN(value) ? context[value] : +value))
```

再次运行，激动人心的绿色按钮再次出现，pass！

## Codewars 精选解

### 1

```js
function simple_assembler(program) {
    const mem = {};
    for (let i = 0; i < program.length; ++i) {
        const [op, reg, val] = program[i].split(' ');
        if (op === 'mov') {
            mem[reg] = isNaN(val) ? mem[val] : +val;
        } else if (op === 'inc') {
            mem[reg] = (mem[reg] || 0) + 1;
        } else if (op === 'dec') {
            mem[reg] = (mem[reg] || 0) - 1;
        } else if (op === 'jnz') {
            if (mem[reg] !== 0) {
                i += val - 1;
            }
        }
    }
    return mem;
}
```

值得学习的是解构赋值方法：

```js
const [op, reg, val] = program[i].split(' ');
```

比我的 `inst[0]` 优雅很多。

### 2

区别于我在 `while` 循环里修改指针，此解在 `jnz` 方法中修改指针，效果相同。

```js
function simple_assembler(program) {
    let pc = 0,
        registers = {};
    const getValue = regOrVal =>
        isNaN(regOrVal) ? registers[regOrVal] : parseInt(regOrVal);
    const instructions = {
        // void：使函数返回 undefined
        mov: (reg, val) => void (registers[reg] = getValue(val)),
        inc: reg => void registers[reg]++,
        dec: reg => void registers[reg]--,
        jnz: (val, distance) => getValue(val) && (pc += parseInt(distance)),
    };

    while (pc < program.length) {
        // 这里我使用解构赋值修改了一下
        let [inst, ...arg] = program[pc].split(' ');
        // instructions[inst](...arg) 返回 undefined 执行 pc++，而 jnz 返回的是指针数字值，被解析为 true，不会执行逻辑或后的表达式
        instructions[inst](...arg) || pc++;
        // 原解
        // let instructionArgs = program[pc].split(' ');
        // instructions[instructionArgs[0]](instructionArgs[1], instructionArgs[2]) || pc++;
    }

    return registers;
}
```

此解涉及到短路求值：

> 作为 `&&` 和 `||` 操作符的操作数表达式，这些表达式在进行求值时，只要最终的结果已经可以确定是真或假，求值过程便告终止，这称之为短路求值（short-circuit evaluation）。这是这两个操作符的一个重要属性。

举几个简单的例子：

```js
0 && console.log(1); // 不执行console.log(1)
1 && console.log(1); // 打印1
null || console.log(1); // 打印1
'a' || console.log(1); // 不执行console.log(1)
```

## 总结

从做题到总结，前后历时 2 天，约 6 小时，虽然吃力，但也学到了很多。

[欢迎 star](https://github.com/hiblacker/codewars-daily)
