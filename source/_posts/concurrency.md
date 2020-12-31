---
title: concurrency
date: 2020-12-02 11:02:28
categories: 'Diary'
tags: 'Diary'
---

## (转载) 如何利用 JavaScript 实现并发控制

原文地址: [https://juejin.cn/post/6912220538286899207?utm_source=gold_browser_extension#heading-4](https://juejin.cn/post/6912220538286899207?utm_source=gold_browser_extension#heading-4)
引申出的FixedArray、HashTable值得大家了解下

### 一、前言

  在开发过程中，有时会遇到需要控制任务并发执行数量的需求。

  例如一个爬虫程序，可以通过限制其并发任务数量来降低请求频率，从而避免由于请求过于频繁被封禁问题的发生。

  接下来，本文介绍如何实现一个并发控制器。

### 二、示例

``` JavaScript

    const task = timeout => new Promise((resolve) => setTimeout(() => {
        resolve(timeout);
    }, timeout))

    const taskList = [1000, 3000, 200, 1300, 800, 2000];

    async function startNoConcurrentControl() {
        console.time(NO_CONCURRENT_CONTROL_LOG);
        await Promise.all(taskList.map(item => task(item)));
        console.timeEnd(NO_CONCURRENT_CONTROL_LOG);
    }

    startNoConcurrentControl();

```
  
  上述示例代码利用 Promise.all 方法模拟6个任务并发执行的场景，执行完所有任务的总耗时为 3000 毫秒。

  下面会采用该示例来验证实现方法的正确性。

### 三、实现

  由于任务并发执行的数量是有限的，那么就需要一种数据结构来管理不断产生的任务。

  队列的「先进先出」特性可以保证任务并发执行的顺序，在 JavaScript 中可以通过「数组来模拟队列」：

``` JavaScript

    class Queue {
        constructor() {
            this._queue = [];
        }

        push(value) {
            return this._queue.push(value);
        }

        shift() {
            return this._queue.shift();
        }

        isEmpty() {
            return this._queue.length === 0;
        }
    }

    对于每一个任务，需要管理其执行函数和参数：

    class DelayedTask {
        constructor(resolve, fn, args) {
            this.resolve = resolve;
            this.fn = fn;
            this.args = args;
        }
    }

    接下来实现核心的 TaskPool 类，该类主要用来控制任务的执行：

    class TaskPool {
        constructor(size) {
            this.size = size;
            this.queue = new Queue();
        }

        addTask(fn, args) {
            return new Promise((resolve) => {
                this.queue.push(new DelayedTask(resolve, fn, args));
                if (this.size) {
                    this.size--;
                    const { resolve: taskResole, fn, args } = this.queue.shift();
                    taskResole(this.runTask(fn, args));
                }
            })
        }

        pullTask() {
            if (this.queue.isEmpty()) {
                return;
            }

            if (this.size === 0) {
                return;
            }

            this.size++;
            const { resolve, fn, args } = this.queue.shift();
            resolve(this.runTask(fn, args));
        }

        runTask(fn, args) {
            const result = Promise.resolve(fn(...args));

            result.then(() => {
                this.size--;
                this.pullTask();
            }).catch(() => {
                this.size--;
                this.pullTask();
            })

            return result;
        }
    }
```
  
  TaskPool 包含三个关键方法：

- addTask: 将新的任务放入队列当中，并触发任务池状态检测，如果当前任务池非满载状态，则从队列中取出任务放入任务池中执行。
- runTask: 执行当前任务，任务执行完成之后，更新任务池状态，此时触发主动拉取新任务的机制。
- pullTask: 如果当前队列不为空，且任务池不满载，则主动取出队列中的任务执行。

![流程图](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0140e0705d364a37bb658a897ecb36c0~tplv-k3u1fbpfcp-watermark.image)

接下来，将前面示例的并发数控制为2个：

``` JavaScript
    const cc = new ConcurrentControl(2);

    async function startConcurrentControl() {
        console.time(CONCURRENT_CONTROL_LOG);
        await Promise.all(taskList.map(item => cc.addTask(task, [item])))
        console.timeEnd(CONCURRENT_CONTROL_LOG);
    }

    startConcurrentControl();
```

执行流程如下：
![流程图](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f52866e0e6b1401e850f3a0ccabdec78~tplv-k3u1fbpfcp-watermark.image)

最终执行任务的总耗时为 5000 毫秒。

### 四、高阶函数优化参数传递

``` JavaScript

    await Promise.all(taskList.map(item => cc.addTask(task, [item])))
```

  手动传递每个任务的参数的方式显得非常繁琐，这里可以通过「高阶函数实现参数的自动透传」：

``` JavaScript

    addTask(fn) {
        return (...args) => {
            return new Promise((resolve) => {
                this.queue.push(new DelayedTask(resolve, fn, args));

                if (this.size) {
                    this.size--;
                    const { resolve: taskResole, fn: taskFn, args: taskArgs } = this.queue.shift();
                    taskResole(this.runTask(taskFn, taskArgs));
                }
            })
        }
    }
```

  改造之后的代码显得简洁了很多：

  await Promise.all(taskList.map(cc.addTask(task)))

五、优化出队操作
  数组一般都是基于一块「连续内存」来存储，当调用数组的 shift 方法时，首先是删除头部元素（时间复杂度 O(1)），然后需要将未删除元素左移一位（时间复杂度 O(n)），所以 shift 操作的时间复杂度为 O(n)。

![执行结果](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5880882cd52483ea5e5752aa4052f9e~tplv-k3u1fbpfcp-watermark.image)

  由于 JavaScript 语言的特性，V8 在实现 JSArray 的时候给出了一种空间和时间权衡的解决方案，在不同的场景下，JSArray 会在 FixedArray 和 HashTable 两种模式间切换。

  在 hashTable 模式下，shift 操作省去了左移的时间复杂度，其时间复杂度可以降低为 O(1)，即使如此，shift 仍然是一个耗时的操作。

  在数组元素比较多且需要频繁执行 shift 操作的场景下，可以通过 「reverse + pop」 的方式优化。

``` JavaScript

    const Benchmark = require('benchmark');
    const suite = new Benchmark.Suite;

    suite.add('shift', function() {
        let count = 10;
        const arr = generateArray(count);
        while (count--) {
            arr.shift();
        }
    })
    .add('reverse + pop', function() {
        let count = 10;
        const arr = generateArray(count);
        arr.reverse();
        while (count--) {
            arr.pop();
        }
    })
    .on('cycle', function(event) {
        console.log(String(event.target));
    })
    .on('complete', function() {
        console.log('Fastest is ' + this.filter('fastest').map('name'));
        console.log('\n')
    })
    .run({
        async: true
    })
```

  通过 benchmark.js 跑出的基准测试数据，可以很容易地看出哪种方式的效率更高：

![执行效率](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6e1954b0030a4f43a2320cb70eab3304~tplv-k3u1fbpfcp-watermark.image)

  回顾之前 Queue 类的实现，由于只有一个数组来存储任务，直接使用 reverse + pop 的方式，必然会影响任务执行的次序。

  这里就需要引入双数组的设计，一个数组负责入队操作，一个数组负责出队操作。

``` JavaScript

    class HighPerformanceQueue {
        constructor() {
            this.q1 = []; // 用于 push 数据
            this.q2 = []; // 用于 shift 数据
        }

        push(value) {
            return this.q1.push(value);
        }

        shift() {
            let q2 = this.q2;
            if (q2.length === 0) {
                const q1 = this.q1;
                if (q1.length === 0) {
                    return;
                }
                q2 = this.q2 = q1.reverse();
            }

            return q2.pop();
        }

        isEmpty() {
            if (this.q1.length === 0 && this.q2.length === 0) {
                return true;
            }
            return false;
        }
    }
```
  
最后通过基准测试来验证优化的效果：

![执行结果](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b55e8b8cb2aa4f6f87ff47da7f607b4b~tplv-k3u1fbpfcp-watermark.image)

作者：descire
链接：<https://juejin.cn/post/6912220538286899207>
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
