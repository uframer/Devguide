# 守护程序

守护程序是跑在后台的进程。在NuttX中守护程序是一个任务，在POSIX（Linux/Mac OS）系统中守护程序是一个线程。

你可以通过`px4_task_spawn()`方法创建新的守护程序。

```C++
daemon_task = px4_task_spawn_cmd("commander",
			     SCHED_DEFAULT,
			     SCHED_PRIORITY_DEFAULT + 40,
			     3600,
			     commander_thread_main,
			     (char * const *)&argv[0]);
```

参数的含义如下：

  * arg0: 进程的名字，这里是`commander`
  * arg1: 调度类型（RR或者FIFO）
  * arg2: 调度优先级
  * arg3: 堆栈大小
  * arg4: 入口函数
  * arg5: 命令行参数
