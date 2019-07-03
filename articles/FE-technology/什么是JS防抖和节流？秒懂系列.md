## 防抖和节流是什么？

是手段。是优化方案。

开发中，经常会遇到一些频繁的事件触发，而频繁的事件触发可能会带来一些弊端，如阻塞 GUI 渲染线程，无法响应用户操作，频繁的接口请求增加服务器压力等等。

而防抖和节流的目的就是优化这些可能出现的弊端。

Talk is cheap, allow me show you the code.

但在这之前你可能需要了解一个概念：cd 。

没错，就是 QWER、复活甲的 cd ：https://baike.baidu.com/item/cd/3516543

## 代码讲解

```js
/**
 * Difference between debounce and throttle
 * In summary, 
 * throttle says, “Hey, we heard you the first time, but if you want to keep going, no problem. 
 * We’ll just ignore you for a while.” 
 * Debounce says, “Looks like you’re still busy. No problem, 
 * we’ll wait for you to finish!”
 */


/**
 * 防抖
 * 
 * @description 只要一直调用, callback 将不会被触发
 * 在一次调用结束后, 只有等待 cd(timeout) 时间, 才能继续调用 callback
 * immediate 决定触发时机
 * 如：输入框校验
 */
function debounce(callback, timeout, immediate) {
  let timer;
  return function () {
    const context = this; // 持有执行上下文
    const args = arguments; // 记录传参
    const later = function () {
      timer = null; // cd 过了，重振旗鼓，重置为初始状态
      if (!immediate) callback.apply(context, args); // 设置为尾部调用才延时触发
    }
    const callNow = immediate && !timer; // 如果确认允许首部调用，且首次调用，那么本次立即触发
    clearTimeout(timer); // 杀掉上次的计时器，重新计时
    timer = setTimeout(later, timeout); // 重启一个计时器，过了 cd 之后才触发
    callNow && callback.apply(context, args); // 设置为首部调用立即触发
  }
}

/**
 * 节流
 * 
 * @description 一直调用 callback, 每隔 cd 时间 callback 触发一次
 * 在 cd 内的调用将不触发
 * @example
 * 1. Throttling a button click so we can’t spam click 控制疯狂按钮的响应频率
 * 2. Throttling an API call 控制 API 的调用频率
 * 3. Throttling a mousemove/touchmove event handler 控制频繁触发事件的相应频率
 */
// solution1 记录时间比较
function throttle(callback, timeout) {
  let triggerTime; // 记录每次真正触发时间
  return function () {
    const context = this; 
    const args = arguments;
    // triggerTime === undefined 为首次调用
    if (triggerTime === undefined || Date.now() - triggerTime > timeout) { // cd 已过
      triggerTime = Date.now(); // 记录真正触发时间
      callback.apply(context, args); // 可以触发回调
    }
  }
}
// solution2 间隔时间反转标志位
function throttle(callback, timeout) {
  let disable; // 触发回调是否禁用
  return function () {
    const context = this; 
    const args = arguments;
    if (!disable) { // 首次调用或者 cd 过了，禁用解除
      callback.apply(context, args); // 可以触发回调
      disable = true; // 马上禁用
      setTimeout(_ => disable = false, timeout); // cd 过了，禁用解除
    }
  }
}
```