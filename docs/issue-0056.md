# MDH 前端周刊第 56 期：Motion 库对比、编码 10X 提效、React 项目结构、Monorepo

<img src="https://tva1.sinaimg.cn/large/e6c9d24ely1h2xruif5l0j21900u0jwy.jpg" style="margin:0;padding:0;vertical-align:middle;" />

<p style="color:gray;text-align:center;margin-bottom:3em;">封面图：carltraw @ unsplash。</p>

<p style="color:blue;font-weight:bold;">Hi，我是云谦，欢迎打开新一期的「MDH：前端周刊」，这是第 0056 期，发表于 2022/06/06。</p>

本周有这些内容想和你分享：

- Framer Motion vs. Motion One
- 编码 10X 提效
- React 项目结构
- Histoire
- Monorepo
- 8 个 HTML 技巧
- OurMetaverse

## Framer Motion vs. Motion One
https://motion.dev/blog/should-you-use-framer-motion-or-motion-one

Framer Motion 和 Motion One 是同一个作者 Matt Perry 的作品，作者做了对比，告诉你在不同场景下应该如何选择。

比如要实现 scale 2 的动画，两者的写法分别是这样的。

```jsx
// Frame Motion
return <motion.div animate={{ scale: 2 }} />

// Motion One
const ref = useRef(null)
useEffect(() => {
  animate(ref.current, { scale: 2 })
}, [])
return <div ref={ref} />
```

Framer Motion 是声明式，Motion One 是命令式。声明式通常写起来更简单，学起来更容易，带来更好的开发者（DX），但没有命令式灵活。比如前者只支持 React，而后者是框架无关的，除了 React，还支持 Vue 和 Angular。

Framer Motion 基于 CSS Transitions、CSS Animations、requestAnimationFrame（作者没写，我猜的），Motion One 基于 [WAAPI](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API)。WAAPI 是新出的 API，在浏览器兼容性和实施问题上需要做很多事，Motion One 希望能处理这些问题，所以 Motion One 的一个定位是 WAAPI 界的 jQuery。基于这个选择，他们所具备的能力也会不同。后者可以在 GPU 上运行动画，可以做数值单位间的动画（比如 px 和 %），前者具备访问每一帧动画的能力。

所以怎么选？如果用 React 但同时对性能没那么注重，用 Frame Motion，否则用 Motion One。

## 编码 10X 提效
https://twitter.com/abeltxor/status/1533018475303604224

推上的一篇高赞 Thread。

1、建立优先级，做正确的事很重要，优先挑影响力大的事，战略上也要勤奋<br />
2、清除环境干扰，比如关掉通知、社交等，同时要学会说「不」，决定「不做什么」也很重要<br />
3、别重复造轮子，善用 npm 依赖，因为社区上可能早有实现，并且通常都比你做地更好<br />
4、找个导师指点方向，可以少踩很多坑<br />
5、尽量自动化，重复的工作怎么能做第二遍呢？<br />
6、学习代码不需要借助教程，多做，然后善用官方文档和 Google 就够了

## React 项目结构
https://www.developerway.com/posts/react-project-structure

先做分解，分解是让 React 应用可扩展的基石。别把项目看成是单一的项目，而是把他看成是一个个独立黑盒功能的组合，各自有自己的 API 供消费者使用。

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2xqprnhq0j21eq0hw0uc.jpg)

然后使用 Monorepo 架构，把功能提取到子包中，以最适合你的项目的方式来组织你的包。

```json
{
  "private": true,
  "workspaces": ["packages/**"]
}
```

单个包内要有层次结构。你可能至少会需要数据层、UI 层和共享层。可以根据需要引入更多，但要注意他们之间需要有明确的界限。

```
/my-feature-package
  /shared
  /ui
  /data
  index.ts
  package.json
```

包之间也要有分层结构。这会让重构变得容易，各层之间有清晰的界限，同时会迫使你的包变大时将其分割成更小的包。

```
/packages
  /core
    /button
    /modal
    /tooltip
    ...
  /product-one
    /footer
    /settings
    ...
  /product-two
    ...
```

Monorepo 中依赖管理是个复杂的话题，但基于 yarn/pnpm 等工具却又非常简单，只要在 package.json 中声明依赖关系即可。

## Histoire
https://github.com/histoire-dev/histoire

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2xh5wlusrj20z20g6wf4.jpg)

一个交互式组件 Playground 的库，类 storybook，但目前仅支持 vue。

## Monorepo
https://www.robinwieruch.de/javascript-monorepos/

一篇非常详细的 Monorepo 科普文，从 Monorepo 的 What、Why 开始讲起，到工具、yarn & pnpm、turborepo、版本更新、CI 和架构，适合入门。

## 8 个 HTML 技巧
https://dev.to/babib/7-shocking-html-tips-you-probably-dont-know-about-ggd

查漏补缺，看下有哪些是不会的。

```html
// 1、捕获摄像头，user 表示前置摄像头，environment 表示后置摄像头
<input type="file" capture="user" accept="image/*" />

// 2、每 10s 刷新一次
<meta http-equiv="refresh" content="10" />

// 3、开启 input 输入框的拼写检测
<input type="text" spellcheck="true" lang="en" />

// 4、上传文件时指定允许的文件格式
<input type="file" accept=".jpeg,.png" />

// 5、阻止浏览器翻译，适用场景比如 Logo 和品牌名
<p translate="no">Brand name</p>

// 6、允许选择多个文件
<input type="file" multiple />

// 7、为 video 标签添加缩略图
<video poster="picture.png"></video>

// 8、声明资源文件的下载
<a href="image.png" download>
```

## OurMetaverse
https://github.com/ourmetaverse/our-metaverse

![](https://tva1.sinaimg.cn/large/e6c9d24egy1h2q8h9pfj0j21m30u0gs0.jpg)

OurMetaverse 以科幻小说《我们的元宇宙·开端》为起点，是 Web3 创作者经济的一次大胆尝试。通过定义了 ERC721M 支持了未来整个作品集的著作权管理以及对应权益分成，拥有 NFT 就拥有了未来 IP 发展的持续收益。项目官网基于 umi 开发，是一个纯前端的 DApp，[官网代码开源](https://github.com/ourmetaverse/our-metaverse)。

区块链 DApp 开发对前端是很友好的，除了智能合约，只需要前端开发就可以构建一个应用。并且应用可以拥有基于区块链的账号体系和经济系统能力。至于智能合约，Solidity 的语法也大量参考了 JS，甚至它的包管理工具也是用 npm。

## 推广

我在知识星球开了个专栏，付费的那种。专栏名叫[「云谦和他的朋友们」](https://mp.weixin.qq.com/s/_23bA1R4t8KiIjCwmr83OQ)。截止 2022.06.06 已有 410+ 朋友加入，写了 127 篇日更，102 篇每日前端资讯简报，还有大量问题回复。

以下是近期的 11 篇日更。

- 127 - 《框架级 codemod》
- 126 - 《一个 CD 构建提速方案》 
- 125 - 《想法：MDH News 信息换信息》
- 124 - 《最近新收获的工具、技巧和经验 03》
- 123 - 《前端速通指南》
- 122 - 《如何提问》
- 121 - 《cnpm 问题两则》 
- 120 - 《手撕源码 11：Master Styles》 
- 119 - 《手撕源码 10：swr（上）》 
- 118 - 《给新人的建议》 
- 117 - 《MFSU V4 要来了吗》 

<p style="color:#b5495b;"><a style="color:#b5495b;" href="https://mp.weixin.qq.com/s?__biz=MjM5NDgyODI4MQ==&mid=2247484448&idx=1&sn=3195bb82d2d2b7d58305c4f1aeae5e0d">点击此处查看详情</a>或扫下方二维码加入。</p>

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2y7g4ptn0j20ku0qy0v4.jpg)

## 周刊一锅端

- [**早早聊的 18 个成长宝藏库**](https://mp.weixin.qq.com/s/3yLbUwqzSy2gFHXkO0PICg)：前端早早鸟，前端早早跑
- [**云谦和他的朋友们**](https://mp.weixin.qq.com/s/NGux3r0P1JJH_z4-vfeksQ)：Umi、Dva 等库作者
- [**DEX 周刊**](https://newsletter.dex.group/)：关于产品、设计、前端、软件的精华资讯邮件列表
- [**前端食堂**](https://mp.weixin.qq.com/s/86Cz3KUWqutu9J0V4tyabQ)：你的前端食堂，吃好每一顿饭

## 小结

如果你喜欢 MDH 前端周刊，请转发给你的朋友，告诉他们[到这里来订阅](https://mp.weixin.qq.com/s?__biz=MjM5NDgyODI4MQ%3D%3D&mid=2247484802&idx=1&sn=caa84339125510680d435a40280a6600)，这是对我最大的帮助。下期见！

<p style="color:#b5495b;">MDH，让开发者有笑容 :)</p>
