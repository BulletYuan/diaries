前天遇到了 

```javascript
function _promiseFn(){
  return new Promise(resolve=>{
    resolve('I am Iron Man!');
  });
}
const _res = await _promiseFn();
console.log(_res); // 'I am Iron Man!'
```

这样的代码，一直百思不得其解： **await如何返回promise后resolve的结果？**

_PS:上面的代码放在代码块中不能运行，毕竟 `await` 没有放在 `async` 函数之中，不过浏览器的控制台是可以直接运行的。我猜测应该是控制台的执行层外面就是 `async` 函数吧？_

其实这个问题的背后更深层的问题就是： **async和await分别做了什么？**

google了一些文章发现[这篇文章](https://javascript.info/async-await)讲的还不错，有兴趣的朋友可以翻译来看看。

=====

# Async

> a function always returns a promise. 

* `async` 函数会自动返回一个 `promise` 类型的方法

* 如果设置了函数返回的值，js也会默认将这个返回值放入到 `promise` 的 `resolve` 结果中

# Await

> makes JavaScript wait until that promise settles and returns its result.

* 会让js“暂停”当前 `async` 函数内的执行

* 会把返回的 `promise` 放入到任务队列中挂起

* 让出主线程（javascript是单线程执行）去执行其他方法，直到该此线程执行完成，才会继续去任务队列中依次取出 `promise` 的返回结果

=====

# 示例

浏览器控制台中输入：

```javascript
function _fn(){
  return new Promise(res=>{
    setTimeout(()=>res(4),1000);
  });
}
console.log(1)
await (
  ()=>{
    setTimeout(()=>console.log(2),0);
  }
)(); // (fn)() 立即执行的匿名函数
console.log(3)
console.log(await _fn())
console.log(5)
setTimeout(()=>console.log(6),0)
```

按照我之前的思想，他的打印应该是：

```javascript
// console.log(1)
// console.log(3)
// console.log(5)
// console.log(2)
// console.log(4)
// console.log(6)
```

现在理解了 `async/await` 的执行机制，`await` 会等待返回 `promise` 的 `resolve` 结果。

那么正确的打印应该是：

```javascript
// console.log(1) -- 正常执行
// console.log(3) -- 正常执行
// console.log(2) -- 返回的延时函数会放在下一次事件循环的0s后执行
// console.log(4) -- 返回的延时函数会放在下一次事件循环的1s后执行
// console.log(5) -- 会因为上个await返回的延时事件阻塞1s
// console.log(6) -- 返回的延时函数会放在再下一次事件循环的0s后执行
```

参考文章

* [The JavaScript language - Async/await](https://javascript.info/async-await)

* [Async/Await替代Promise的6个理由](https://blog.fundebug.com/2017/04/04/nodejs-async-await/)