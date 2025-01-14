# MDH 前端周刊第 49 期：Lexical、TS Compiler、:has、Astro SSR、deno everything

<img src="https://tva1.sinaimg.cn/large/e6c9d24ely1h1cz3oj1rnj21900u0tc2.jpg" style="margin:0;padding:0;vertical-align:middle;" />

<p style="color:gray;text-align:center;margin-bottom:3em;">封面图：chrishardyphotography @ unsplash.com。</p>

<p style="color:blue;font-weight:bold;">Hi，我是云谦，欢迎打开新一期的「MDH：前端周刊」，这是第 0049 期，发表于 2022/04/18。</p>

本期主要内容有这些：

- Lexical
- TS Compiler 原理
- `:has()` 选择器
- Astro SSR
- deno everything
- React 18 速读
- TS 日期类型
- 33 个 JavaScript 概念

## Lexical
https://github.com/facebook/lexical

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h1ctf3ey63j21k40p444t.jpg)

Lexical 是 Facebook 新开源的富文本编辑器框架，主打可扩展性、可靠性、可访问性和性能。核心包仅 22kb（min+gzip），额外的能力和尺寸按需添加。在支持懒加载的框架中，我们可以将插件的加载推迟到用户和编辑器交互时进行，以进一步提升性能。

Lexical 可以用在哪？

1、比 textarea 复杂点的需求，比如需要 mention、自定义 emoji、链接和 hashtag 等
2、适用于 Blog、社交软件、消息应用的富文本编辑器
3、完整的适用于 CMS 的所见即所得编辑器
4、有实时协作需求的文本编辑

内置提供了 React 实现和大量插件，我们可通过以下步骤在 React 项目中尝鲜。

```bash
# 先安装依赖
$ pnpm i lexical @lexical/react
```

封装 Editor 组件。

```js
import LexicalComposer from '@lexical/react/LexicalComposer';
import LexicalPlainTextPlugin from '@lexical/react/LexicalPlainTextPlugin';
import LexicalContentEditable from '@lexical/react/LexicalContentEditable';
import LexicalOnChangePlugin from '@lexical/react/LexicalOnChangePlugin';

function Editor() {
  return (
    <LexicalComposer initialConfig={{}}>
      <LexicalPlainTextPlugin
        contentEditable={<LexicalContentEditable />}
        placeholder={<div>Enter some text...</div>}
      />
      <LexicalOnChangePlugin onChange={onChange} />
    </LexicalComposer>
  );
}
```

## TS Compiler 原理
https://www.huy.rocks/everyday/04-01-2022-typescript-how-the-compiler-compiles

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h1cpsqw8mtj20ub0u0go6.jpg)

这篇是作者观看「How the TypeScript compiler compiles」视频后的阅读笔记，。TypeScript 编译时（即调用 tsc 时）会经过 Program、Parser、AST、Binder、Emitter、Scanner、Type Checker 的过程，每个环节在 typescript 仓库的 src/compiler 目录下都能找到对应的文件名实现。

相比大家熟悉的 Babel 有几点大的不同，1）有 Program 环节，TypeScript 会根据 include 配置分析所有需要引入的文件及其依赖，所以是项目级，而 Babel 是文件级的，2）Emitter 环节除了产出 js 及其 sourcemap 文件，还会产出 dts 类型定义文件。

## :has() 选择器
https://matthiasott.com/notes/css-has-a-parent-selector-now
https://ishadeed.com/article/css-has-parent-selector/

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h1ct3byudsj21i80oagr5.jpg)

CSS 中一直没有父选择器，即当子节点满足一定条件时给父节点加样式。这不仅是出于性能考虑，而且还和浏览器渲染机制有关，当子节点渲染后需要 repaint 父节点，而且可能是很多的父节点，这本该是不可能完成的任务，在 Safari 15.4 里通过 `:has()` 选择器解了，这也是目前唯一支持的浏览器，Chrome 101 开始可通过 Flag 开启。

`:has()` 属于 CSS Level 4 的选择器，官方命名是 [Relational Pseudo-class（关系伪类）](https://www.w3.org/TR/selectors-4/#relational)。除了选择父元素，还可以做其他的，比如选择兄弟节点。以下是一些示例。

```css
// 匹配包含 svg 的 button 元素，通常是 icon
button:has(> svg)
// 匹配包含选中的 checkbox 的 form 元素
form:has(input[type="checkbox"]:checked)
// 匹配包含两个选中的 checkbox 的 form 元素，以此类推
form:has(input[type="checkbox"]:checked ~ input[type="checkbox"]:checked)
// 匹配不包含 h1-h6 的 section 元素
section:not(:has(h1, h2, h3, h4, h5, h6))
// 匹配包含 caption 描述的图片（兄弟节点）
figure img:has(+ figcaption)
```

此外，可通过 Feature Test 针对不支持 `:has()` 的浏览器做提示，然后支持时隐藏提示。

```css
@supports selector(:has(*)) {
  .has-selector-warning { display: none; }
}
```

## Astro SSR
https://astro.build/blog/experimental-server-side-rendering/

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h1cuh7w6gvj22080u076y.jpg)

Astro SSR 和我理解的 SSR 不同，好像只有 SS 没有 R。由于 Astro 特别之处是只跑在服务端，所以一些动态的能力会直接在服务端处理完成，好处是更快。

比如权限校验。

```astro
---
import { getUser } from '../api/index.js';
const user = await getUser(Astro.request);
if (!user) {
  return Astro.redirect('/login');
}
---
<h1>Hello {user.name}</h1>
```

## Web Components From React
https://spin.atomicobject.com/2022/04/11/export-web-components/

作者最近用 React 重写了一个 Vue 的老系统，但发现一些重构的 React 组件能解 Vue 老系统的问题，所以面临的问题是「如何从 React 应用中导出组件给 Vue 应用使用」。

解法是通过 Web Component，把 React 组件打包通过以下代码打包为 Web Component。

```js
import ReactDOM from 'react-dom';
const Foo = (props) => <h1>Hello {props.name}</h1>
class FooComponent extends HTMLElement {
  connectedCallback() {
    const root = document.createElement('div');
    this.attachShadow({ mode: 'open' }).appendChild(root);
    const name = this.getAttribute('name');
    RenderDOM.render(<Foo name={name}>, root);
  }
}
window.customELements.get('foo-component') ||
window.customElements.define('foo-cumponent', FooComponent);
```

然后通过 webpack 打包成单独的 js 文件嵌入到 HTML 中使用。

```html
<script src="/path/to/js"></script>
<foo-component name="bar" />
```

## deno everything
https://www.sitepen.com/blog/doing-it-all-with-deno

作者整理了 [SitePen/deno-todos-blog](https://github.com/SitePen/deno-todos-blog) 脚手架，通过示例的 todo 项目，演示包含 client、server、test、lint、deploy 的全栈技术栈。

## React 18 速读
https://dev.to/shrutikapoor08/react-18-quick-guide-core-concepts-explained-519p

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h1cwr95tfdj21240pgad5.jpg)

上图是 React 18 新引入的概念、特性、API 和 Hooks。React 18 在 47 期也介绍过一次，这里再补充下 Suspense + SSR 的部分。

React 17 及之前在 Server 端渲染 React 组件是全部一起渲染后返回，如果其中组件返回慢，整个页面都会慢。期望的方式是和客户端一样，通过 `import()` 把慢的组件拆出去，不影响整体页面的渲染。React 17 也有 Suspense + Lazy 的方式，但不支持 SSR，React 18 对此做了支持，可以把慢的依赖拆出去。

作者没有提的一点是，在 React 18 之前，虽然官方没有给方案，但社区方案是[通过 loadable 来同时支持服务端和客户端的 code splitting](https://medium.com/priceline-labs/route-based-code-splitting-with-loadable-components-and-webpack-76c57865f0ca)。

## TS 日期类型
https://blog.logrocket.com/handing-date-strings-typescript/

TypeScript 没有 DateString 类型，所以要传 `YYYY-MM-DD` 或 `YYYYMMDD` 类型的日期时只能用字符串，问题是不严谨，任意字符串比如 `"dog"` 也会被识别。

解法是用 Template literal 类型手动实现一个。

```ts
type oneToNine = 1|2|3|4|5|6|7|8|9;
type zeroToNine = 0|1|2|3|4|5|6|7|8|9;
type YYYY = `19${zeroToNine}${zeroToNine}` | `20${zeroToNine}${zeroToNine}`;
type MM = `0${oneToNine}` | `1${0|1|2}`;
type DD = `${0}${oneToNine}` | `${1|2}${zeroToNine}` | `3${0|1}`;
type DateString = `${YYYY}${MM}${DD}`;
```

## 33 个 JavaScript 概念
https://dev.to/eludadev/33-javascript-concepts-every-beginner-should-know-with-tutorials-4kao

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h1cy8b1abbj20u018lk18.jpg)

非常好的入门教程，适用于初学者。

## 发布

以下是上周社区的重要发布。

- [Umi 发布 4.0.0-rc.12](https://github.com/umijs/umi-next/releases/tag/v4.0.0-rc.12)，包含新增 umi preview、支持 browser terminal、内置 stylelint 和 eslint 等 9 项更新
- [react-redux 发布 8.0.0](https://github.com/reduxjs/react-redux/releases/tag/v8.0.0)，基于 TypeScript 重写，同时让 useSelector、connect 和 `<Provider>` 兼容 React 18
- [Astro 发布 1.0 Beta](https://astro.build/blog/astro-1-beta-release/)
- [收到 CodeSandbox Project Beta](https://codesandbox-app.notion.site/CodeSandbox-Projects-9ab3cd0a16c4437a88f839d801034e6b)，尝试了 umi-next 和 umi 仓库都没成功，暂时放弃
- [Nx 发布 13.10](https://blog.nrwl.io/what-is-new-in-nx-13-10-a58d2051d73)
- [Turbo 发布 1.2](https://turborepo.org/blog/turbo-1-2-0)
- [react-dnd 发布 8.0.0](https://github.com/react-dnd/react-dnd/releases/tag/v16.0.0)，ESM only，支持 Node 18
- [Slidev 发布 0.30.0](https://github.com/slidevjs/slidev/releases/tag/v0.30.0)，内置 vue-starport
- [Create React App 发布 5.0.1](https://github.com/facebook/create-react-app/releases/tag/v5.0.1)，兼容 react 18
- [Webstorm 发布 2022.1](https://blog.jetbrains.com/webstorm/2022/04/webstorm-2022-1/)

## 推广

我在知识星球开了个专栏，付费的那种。专栏名叫[「云谦和他的朋友们」](https://mp.weixin.qq.com/s/_23bA1R4t8KiIjCwmr83OQ)。截止 2022.04.04 已有 300+ 朋友加入，写了 97 篇日更，77 篇每日前端资讯简报，还有大量问题回复。

以下是这两周更新的 Umi 4 系列日更。

- 97 - 《Umi 4 特性 09：Umi UI 卷土重来？》
- 96 - 《Umi 4 特性 08：SSR & SSG》
- 95 - 《Umi 4 特性 07：微生成器》
- 94 - 《Umi 4 特性 06：那些小而美的改进（上）》
- 93 - 《Umi 4 特性 05：稳定白盒性能好的 ESLint》
- 92 - 《Umi 4 特性 04：build 阶段的构建提速》
- 91 - 《Umi 4 特性 03：默认最快的请求》
- 90 - 《Umi 4 特性 02：React Router 6 和新路由》
- 89 - 《Umi 4 特性 01：MFSU V3》

<p style="color:#b5495b;"><a style="color:#b5495b;" href="https://mp.weixin.qq.com/s?__biz=MjM5NDgyODI4MQ==&mid=2247484448&idx=1&sn=3195bb82d2d2b7d58305c4f1aeae5e0d">点击此处查看详情</a>或扫下方二维码加入。</p>

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h08blrtribj20sr12rgpn.jpg)

## 周刊一锅端

- [**早早聊的 18 个成长宝藏库**](https://mp.weixin.qq.com/s/3yLbUwqzSy2gFHXkO0PICg)：前端早早鸟，前端早早跑
- [**云谦和他的朋友们**](https://mp.weixin.qq.com/s/NGux3r0P1JJH_z4-vfeksQ)：Umi、Dva 等库作者
- [**DEX 周刊**](https://newsletter.dex.group/)：关于产品、设计、前端、软件的精华资讯邮件列表
- [**前端食堂**](https://mp.weixin.qq.com/s/86Cz3KUWqutu9J0V4tyabQ)：你的前端食堂，吃好每一顿饭

## 小结

以上就是本期我的分享。如果需要文内资讯的链接，请点击「查看原文」进入语雀查看。持续更新不易，如果你喜欢本周刊，请转发给你的朋友，告诉他们到这里来订阅，这是对我最大的帮助。下期见！

<p style="color:#b5495b;">MDH，让开发者有笑容 :)</p>
