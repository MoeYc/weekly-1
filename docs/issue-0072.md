# MDH 前端周刊第 72 期：Qwik City、Umi x Valtio、再见 useEvent、React 18 快在哪

<img src="https://tva1.sinaimg.cn/large/006y8mN6ly1h6q5smyvbdj31900u041e.jpg" style="margin:0;padding:0;vertical-align:middle;" />

<p style="color:gray;text-align:center;margin-bottom:3em;">封面图：nicholasjio @ unsplash。</p>

<p style="color:blue;font-weight:bold;">Hi，我是云谦，欢迎打开新一期的「MDH：前端周刊」，这是第 0072 期，发表于 2022/10/03。<span style="color:red;font-weight:bold;">同时，从本期开始，准备尝试个小规则，如果「文章点赞量 >= 50」，则继续周更继续更新，否则，就偷个懒，隔一周更新。</span></p>



本周有这些内容想和你分享：

* Qwik City
* Umi x Valtio
* Chrome 发布流程
* 再见 useEvent
* React 18 快在哪
* TypeScript 入门教程
* Dracula UI
* VSCode 宠物插件

## Qwik City
https://www.builder.io/blog/qwik-and-qwik-city-have-reached-beta

![](https://cdn.builder.io/api/v1/image/assets%2FYJIGb4i01jvw0SRdL5Bt%2F5f8db18f68c74f6f9919f3877b6246b4?format=webp&width=2000)

Qwik 在推出 Beta 版时，同时推出了他们的框架 Qwik City。

Qwik City 是 Qwik 配套的元框架，和 Remix、Next.js、Nuxt 是同一类。Qwik City 提供了约定式路由、数据获取、捆绑优化、预加载、流媒体以及各 Edge Function 服务商的支持。

快速上手。

```bash
$ npx create qwik@latest
$ cd qwik-app
$ npm start
```

## Umi x Valtio
https://umijs.org/docs/max/valtio

Umi 在 4.0.23 中加入了 Valtio 插件。这是在大量调研之后，基于中台项目的场景做的决定，使用 Valtio 作为下一代的数据流。

为啥是 valtio？

valtio 的特点是外部多 Store + 基于 Proxy。1）个人用不惯 jotai 和 recoil 这种 react 内部原子化的数据流方案，感觉和被 redux 培养起来的心智模型有冲突，所以会更倾向于外部 Store 一些，2）由于场景是中后台，对于兼容性的容忍度比较高，比如不用兼容 IE11，所以完全可以用基于 Proxy 的数据流方案，这类数据流方案在更新数据和读取数据时都更简单。

为啥不是 zustand？

1、zustand 和 valtio 是同一个作者写的，功能覆盖上其实比较类似，最大的区别是 valtio 基于 proxy 而 zustand 不是。个人有几个方面的考虑，1）更新数据，2）读取数据，3）类型提示。

2、更新数据的方式更符合人性，比如可以直接 `state.todos['321'].completed = true`，而不用 `setState(todos => ({ …todos, 321: { …todos['321'], completed: true } }))`。当然，这一点非 proxy 方案可基于 immer 实现和 proxy 方案类似的操作。

3、读取数据默认高性能，无需 selector。非 proxy 方案比如 react-redux 和 zustand 为了性能优化，避免不必要的 re-render，通常会通过 selector 选择 store 的一部分，这会带来不必要的心智负担。基于 Proxy 的方案是响应式的，无需 selector，默认高性能。

4、类型提示的差异主要在扩展上。valtio 用的是组合式，zustand 用的是 middleware。没具体试过 zustand 的 middleware，但个人理解，理论上 middleware 的类型提示没有 valtio 友好。比如 valtio 的 proxyWithHistory 会把数据结构改成 `{ value, history, redo, undo, … }` 这种，在类型提示上可以完美衔接。

## Chrome 发布流程
https://developer.chrome.com/en/blog/chrome-updates/

![](https://wd.imgix.net/image/kheDArv5csY6rvQUJDbWRscckLr1/hlWZ6vcwjIBjagdnkVUS.jpeg?auto=format&w=1600)

Chrome 每个月都会更新版本，他们是如何确保发布稳定性和修复问题的，以下是一些摘录。

1、分 Canary、Dev、Beta 和 Stable 共 4 个发布频道，每个频道都有不少 Chrome 用户  
2、密切关注用户和开发人员在社交媒体、媒体文章和错误报告中的言论和反馈的问题  
3、有几轮测试，先是有持续运行的自动化系统，然后也有测试团队来手动查找错误  
4、新版本不会立即推送给所有用户，当发现问题时，会停止推送  
5、安全问题遵循 [Project Zero Disclosure](https://googleprojectzero.blogspot.com/2021/04/policy-and-disclosure-2021-edition.html) 政策，目标是 7 天内向 Stable 频道的用户发布修复

## 再见 useEvent
https://github.com/reactjs/rfcs/pull/220#issuecomment-1259938816

useEvent 原计划解两个问题，1）渲染优化，2）useEffect 重新触发问题。但是发现没办法一下子做两件事。于是 useEvent RFC 废弃，这两个问题会拆开了来解。渲染优化问题倾向用 React Forgot 解，useEffect 重新触发问题会通过另一个专门解此问题的 RFC 来解，命名上应该不再会用 useEvent。

## React 18 快在哪
https://www.reddit.com/r/reactjs/comments/xmr9tg/comment/ippsuin/

主要是 3 点。

1、由于 React 18 有 [Automatic Batching](https://github.com/reactwg/react-18/discussions/21)，多个 setState 只会触发一次渲染，而在 18 之前，setState 几次就会渲染几次  
2、React 18 引入了[新的服务器渲染架构](https://github.com/reactwg/react-18/discussions/37)，会带来显著的性能提升，请检查你的框架是否支持  
3、React 18 引入了用于把状态更新标记为可终端的 [startTransition](https://github.com/reactwg/react-18/discussions/41)，虽然不能让页面变快，但如果用户在可中断的状态更新中点击，会让你的应用[更具有响应性](https://github.com/reactwg/react-18/discussions/65)

## TypeScript 入门教程
https://www.totaltypescript.com/tutorials/beginners-typescript

![](https://tva1.sinaimg.cn/large/006y8mN6ly1h6q47uf4njj30f30kqmy9.jpg)

Matt 的免费 TypeScript 入门教程，以问题和答案的形式。看完入门教程，还可以看看他的 TypeScript Tips 系列，包含不少有用的进阶小技巧。

## Dracula UI
https://ui.draculatheme.com/

![](https://tva1.sinaimg.cn/large/006y8mN6ly1h6q5kp8qt1j30xc0hiaaw.jpg)

刚开源，特点如下。

1、**为黑暗模式而建**。大多数模板都是使用浅色的，后来才改成深色的。深色主题不应该是一个事后的想法，它们应该是一个首要任务。

2、**设计师友好**。通过使用一个高度可配置的设计系统，加快原型设计阶段。通过利用精心制作的Figma文件，轻松进行合作。

3、**友好的开发者体验**。不用担心类名，只需使用 Visual Studio 的代码片段。你可以利用自动完成的优势，也可以从你的代码编辑器中直接访问整个文档。

## VSCode 宠物插件
https://marketplace.visualstudio.com/items?itemName=tonybaloney.vscode-pets

![](https://github.com/tonybaloney/vscode-pet/raw/master/screenshots/screenshot.gif)

装个宠物插件，提升开发体验，哈哈。在你的代码编辑器中放入一只无聊的小猫、一只热情的狗、一条好动的蛇、一只橡皮鸭或 Clippy，还能互动。。


## 周刊一锅端

- [**早早聊的 18 个成长宝藏库**](https://mp.weixin.qq.com/s/3yLbUwqzSy2gFHXkO0PICg)：前端早早鸟，前端早早跑
- [**云谦和他的朋友们**](https://mp.weixin.qq.com/s/NGux3r0P1JJH_z4-vfeksQ)：Umi、Dva 等库作者
- [**DEX 周刊**](https://newsletter.dex.group/)：关于产品、设计、前端、软件的精华资讯邮件列表
- [**前端食堂**](https://mp.weixin.qq.com/s/86Cz3KUWqutu9J0V4tyabQ)：你的前端食堂，吃好每一顿饭

## 小结

如果你喜欢 MDH 前端周刊，请转发给你的朋友，告诉他们[到这里来订阅](https://mp.weixin.qq.com/s?__biz=MjM5NDgyODI4MQ%3D%3D&mid=2247484802&idx=1&sn=caa84339125510680d435a40280a6600)，这是对我最大的帮助。下期见！

<p style="color:#b5495b;">MDH，让开发者有笑容 :)</p>
