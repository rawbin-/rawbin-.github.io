---
layout: post
title: "JavaScript 事件循环源码分析"
categories: [Web开发,前端开发,JavaScript]
tags: [JavaScript,事件循环,event-loop,源码]
---



### NodeJS中的异步实现

+ [`libuv` 设计文档](http://docs.libuv.org/en/v1.x/design.html)
+ [`libuv` API](http://docs.libuv.org/en/v1.x/api.html)

###   `libuv`中的事件阶段

+ 执行到点的定时器
+ 执行`pending`回调
+ 执行`idle` 操作
+ 执行`prepare`操作
+ 监听`I/O`请求
+ 执行`check`操作
+ 执行`close`回调




### 源码分析`libuv `分平台`core.c`

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






### 参考资料

1.  [libevent github](https://github.com/libevent/libevent)
2.  [libevent org](http://libevent.org/)
3.  [libuv github](https://github.com/libuv/libuv)
4.  [libuv org](http://libuv.org/)

