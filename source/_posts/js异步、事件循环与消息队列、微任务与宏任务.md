---
title: Js异步、事件循环与消息队列、微任务与宏任务
date: 2020-01-11 10:01:00
tags: ES6
---

```
  setTimeout(()=>{
    console.log(1) // 8
  },0)

  console.log(2)  // 1

  new Promise((resolve, reject) => {
    console.log(3) // 2
    resolve()
    console.log(4) // 3
  }).then(res=> {
    console.log(5) // 5
    return new Promise((resolve,rej) => {
      resolve()
    })
  }).then(res => {
    console.log(6) // 7
  })
  console.log(7) // 4

```

<!-- more -->

```

  setTimeout(()=>{
    new Promise((r, j) => {
      console.log(11) // 9
      r()
    }).then(res => {
      console.log(8) // 11
    })
    console.log(9) // 10
  },0)

  new Promise((r,j)=> {
    r()
  }).then(res=> {
    console.log(10) // 6
  })

```

  >> 1.执行一个宏任务（栈中没有就从事件队列中获取）
  >> 2.执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
  >> 3.宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
  >> 4.当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
  >> 5.渲染完毕后，JS引擎线程继续，开始下一个宏任务（从宏任务队列中获取）

----
  总结的很好一篇文章：

  > [掘金:总结：JavaScript异步、事件循环与消息队列、微任务与宏任务](https://juejin.im/post/5be5a0b96fb9a049d518febc)
