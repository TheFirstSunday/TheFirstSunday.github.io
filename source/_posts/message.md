---
title: 四种跨浏览器选项卡实时通信方法
categories: 'Diary'
tags: 'Diary'
---

多年来，随着对 Web 应用程序的需求增多，Web 浏览器的功能也不断增强。从而，可以找到实现类似功能的多种方法。本文将介绍一类很少被人关注的功能：在浏览器选项卡之间进行通信。以下是列举的几个适用场景：

- 将对应用程序的主题修改（例如，深色或浅色主题）应用到所有已打开的浏览器选项卡中。
- 请求用于身份验证的最新令牌，并在浏览器选项卡之间共享。
- 跨浏览器选项卡同步应用程序状态。

本文主要介绍几种跨浏览器通信的方法。然而，每种方法都有其优缺点。因此，本文会详细讨论它们，以便让您能够在实际开发中找到适用的最佳方法。

1. 使用本地存储事件 LocalStorage

通过使用 LocalStorage，可以使得同一应用程序源中的选项卡之间进行通信。同时 LocalStorage 也支持事件，可以使用此功能跨浏览器选项卡进行通信，存储更新后，其他选项卡将接收事件。

例如，在一个选项卡中执行以下 JavaScript 代码。

```
    window.localStorage.setItem("theme", "dark");
```

如下所示，监听事件的其他选项卡将接收它：

```
    window.addEventListener('storage', (event) => {
        if (event.storageArea != localStorage) return;
        if (event.key === 'theme') {
            // 测试使用 event.newValue
        }
    });
```

[codesandbox demo](https://codesandbox.io/s/bold-hoover-ol2jz?file=/index.html)

2. 使用 BroadcastChannel API 接口
   
BroadcastChannel API 允许选项卡、窗口、Frames、Iframes 和 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 之间的通信。一个选项卡可以创建一个 Channel 并在其中发送消息，如下所示：

```
    const channel = new BroadcastChannel('app-data');
    channel.postMessage('post');
```

其他选项卡可以监听频道，如下所示：

```
    const channel = new BroadcastChannel('app-data');

    channel.addEventListener ('message', (event) => {
        console.log(event.data);
    });

    // Disconnect the channel
    channel.close();

```

[codesandbox demo](https://codesandbox.io/s/white-hooks-9l081?file=/index.html)

这样，浏览器上下文（**Windows、Tabs、Frames、或 Iframes**）之间可以进行通信。尽管这是浏览器选项卡之间的一种很便捷的通信方式，但 safari 和 IE 是不支持这种方式的。可以在 MDN 的 BroadcastChannel 文档中查看详细信息。

3. 使用 Service Worker 发送消息

可能会有疑问，Service Worker 是如何进入这种场景的。不过从根本上来说，Service Worker 支持发送消息，可以使用这些消息在浏览器选项卡之间进行通信。
使用 Service Worker，可以发送如下所示的消息：

```
    navigator.serviceWorker.controller.postMessage({
        broadcast: data
    });
```

同时在接收 Worker 的其他浏览器选项卡中可以监听事件。

```
    addEventListener('message', async (event) => {
        if ('boadcast' in event.data ) {
            const allClients = await clients.matchAll();
            for (const client of allClients) {
                client.postMessage(event.broadcast);
            }
        }
    });
```

这种方法提供更多的控制保障，是传递消息的可靠方法。但是，实现 Service Worker 需要一些关于 Service Worker API 的补充知识和额外工作。所以，在这种情况下，如果其他方法都不起作用，最好还是尝试这个方法。可以在 MDN 的 [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerRegistration) 文档中找到更多信息，还有一个完整的示例。

4. 使用 Service Worker 发送消息

Window.postMessage() 方法是跨浏览器选项卡、弹出窗口和 Iframes 进行通信的传统方法之一。可以按如下方式发送消息.
**Syntax** 
targetWindow.postMessage(message, targetOrigin, [transfer]);

```
    const targetWindow = window.opener;
    targetWindow.postMessage(message, targetOrigin)
```

目标窗口可以监听事件，如下所示：

```
    window.addEventListener("message", (event) => {
        if (event.origin !== "http://localhost:8080") return;
        // 可以做测试
    }, false);
```

与其他方法相比，这种方法有一个优点：可以支持跨源通信。但它也有一个限制：需要引用另一个浏览器选项卡。所以这种方法只适用于通过 window.open() 或 document.open() 方法。可以在 MDN 文档中找到更多信息。

[codesandbox demo](https://codesandbox.io/s/gallant-mendeleev-jnbuo?file=/index.html)