---
layout: post
title: "JavaScript 事件循环源码分析"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,事件循环,event-loop,源码]
---



### 1 NodeJS中的异步实现

+ [`libuv` 设计文档](http://docs.libuv.org/en/v1.x/design.html)
+ [`libuv` API](http://docs.libuv.org/en/v1.x/api.html)



### 2 `libuv`中的事件阶段

+ 执行到点的定时器
+ 执行`pending`回调
+ 执行`idle` 操作
+ 执行`prepare`操作
+ 监听`I/O`请求
+ 执行`check`操作
+ 执行`close`回调




### 3 源码分析

#### 3.1 `nodejs` 主文件`/src/node.cc`

```
do {
      v8_platform.PumpMessageLoop(isolate);
      more = uv_run(env.event_loop(), UV_RUN_ONCE);

      if (more == false) {
        v8_platform.PumpMessageLoop(isolate);
        EmitBeforeExit(&env);

        // Emit `beforeExit` if the loop became alive either after emitting
        // event, or after running some callbacks.
        more = uv_loop_alive(env.event_loop());
        if (uv_run(env.event_loop(), UV_RUN_NOWAIT) != 0)
          more = true;
      }
    } while (more == true);
```



#### 3.2 `libuv `分平台`/deps/uv/src/unix/core.c`

```
int uv_run(uv_loop_t* loop, uv_run_mode mode) {
  int timeout;
  int r;
  int ran_pending;

  r = uv__loop_alive(loop);
  if (!r)
    uv__update_time(loop);

  while (r != 0 && loop->stop_flag == 0) {
    uv__update_time(loop);
    uv__run_timers(loop);
    ran_pending = uv__run_pending(loop);
    uv__run_idle(loop);
    uv__run_prepare(loop);

    timeout = 0;
    if ((mode == UV_RUN_ONCE && !ran_pending) || mode == UV_RUN_DEFAULT)
      timeout = uv_backend_timeout(loop);

    uv__io_poll(loop, timeout);
    uv__run_check(loop);
    uv__run_closing_handles(loop);

    if (mode == UV_RUN_ONCE) {
      /* UV_RUN_ONCE implies forward progress: at least one callback must have
       * been invoked when it returns. uv__io_poll() can return without doing
       * I/O (meaning: no callbacks) when its timeout expires - which means we
       * have pending timers that satisfy the forward progress constraint.
       *
       * UV_RUN_NOWAIT makes no guarantees about progress so it's omitted from
       * the check.
       */
      uv__update_time(loop);
      uv__run_timers(loop);
    }

    r = uv__loop_alive(loop);
    if (mode == UV_RUN_ONCE || mode == UV_RUN_NOWAIT)
      break;
  }

  /* The if statement lets gcc compile it to a conditional store. Avoids
   * dirtying a cache line.
   */
  if (loop->stop_flag != 0)
    loop->stop_flag = 0;

  return r;
}
```





### 4 参考资料

1.  《深入浅出NodeJS》
2.  [深入理解Node.js：核心思想与源码分析](https://github.com/yjhjstz/deep-into-node)
3.  [深入理解Node.js：核心思想与源码分析](https://yjhjstz.gitbooks.io/deep-into-node/)
4.  [node源码详解（二 ）—— 运行机制 、整体流程](https://cnodejs.org/topic/56e3be21f5d830306e2f0fd3)
5.  [从Chrome源码看浏览器的事件机制](https://zhuanlan.zhihu.com/p/25095179?refer=dreawer)
6.  [事件循环机制 Event-Loop及其延伸](https://github.com/amandakelake/blog/issues/26)
7.  [【朴灵评注】JavaScript 运行机制详解：再谈Event Loop](https://blog.csdn.net/lin_credible/article/details/40143961)
8.  [一次弄懂Event Loop（彻底解决此类面试问题）](https://juejin.im/post/5c3d8956e51d4511dc72c200?utm_source=gold_browser_extension)
9.  [Event loop in JavaScript](https://acemood.github.io/2016/02/01/event-loop-in-javascript/)
10.  [libevent github](https://github.com/libevent/libevent)
11.  [libevent org](http://libevent.org/)
12.  [libuv github](https://github.com/libuv/libuv)
13.  [libuv org](http://libuv.org/)
14.  [Node.js挖掘系列](https://cnodejs.org/topic/5594ada26ba28efa30a604e2)

