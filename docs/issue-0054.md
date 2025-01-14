# MDH 前端周刊第 54 期：px 还是 rem、devjar、AutoAnimate、JS 概念图解

<img src="https://tva1.sinaimg.cn/large/e6c9d24ely1h2hlf3yd9ej21900u0gu4.jpg" style="margin:0;padding:0;vertical-align:middle;" />

<p style="color:gray;text-align:center;margin-bottom:3em;">封面图：unarchive @ unsplash。</p>

<p style="color:blue;font-weight:bold;">Hi，我是云谦，欢迎打开新一期的「MDH：前端周刊」，这是第 0054 期，发表于 2022/05/23。</p>

本周有这些内容想和你分享：

- px 还是 rem
- devjar
- AutoAnimate
- AntV S2
- Git ignore .gitignore
- JS 概念图解

## px 还是 rem
https://www.joshwcomeau.com/css/surprising-truth-about-pixels-and-accessibility/

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2gyat24geg20iv0a6alc.gif)

如果要考虑可访问性，px 和 em/rem 应该如何选择？px 是最不抽象的单位，就是屏幕上的一个点；em 是基于字体大小的相对单位，并且是个比例；rem 是 em 类似，但基于的字体值始终是 `<html>` 元素上的值。

放大页面有两种形式。一是 `⌘+加号` 放大，WCAG 要求网站在 200% 缩放时是可用的，但通常视障朋友会放更大；二是 Font Scaling，各浏览器都可配置基础的字体大小，见上图。

那使用什么单位可以保证放大效果下的可用性？答案是 By Scense，不同场景下要求不同，不是非此即彼，在整体用 rem 的基础上，一些微场景，有时 px 会更好，有时 em/rem 会更好。比如 padding 用 px 更好、media query 用 rem 更好、文本间的垂直间距用 em 更好、按钮宽度大概率用 rem 更好（注意加 max-width 约束）。

rem 虽好，但不容易记。作者最后还推荐了基于 css variable 使用 rem 的方法。

```css
html {
  --14px: 0.875rem;
  --15px: 0.9375rem;
  --16px: 1rem;
  --17px: 1.0625rem;
  --18px: 1.125rem;
  --19px: 1.1875rem;
  --20px: 1.25rem;
  --21px: 1.3125rem;
}
h1 {
  font-size: var(--21px);
}
```

## devjar
https://github.com/huozhi/devjar

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2gtvtenamj21360qwgnk.jpg)

devjar 是跑在浏览器中的 esm bundless 代码预览器。作者是 [huozhi](https://github.com/huozhi)，来自 vercel。

翻了翻源码，关键功能包含这些。用 [es-module-shims](https://github.com/guybedford/es-module-shims) 支持 import-maps；传入的代码模块转化成 Inline Modules；用 [sucrase](https://github.com/alangpierce/sucrase) 实时编译代码，支持 jsx 和 typescript；用 [es-module-lexer](https://github.com/guybedford/es-module-lexer) 提取 import 并替换路径为 cdn 上 esm 格式的地址，同时支持 externals。

四步尝鲜，

```js
// 第一步：import
import { useLiveCode } from 'devjar';

// 第二步：使用 useLiveCode 拿到 ref 和 load
const { ref, error, load } = useLiveCode({ getModulePath(m) { return `https://cdn.skypack.dev/${m}` } });

// 第三步：把 ref 传给 iframe
<iframe ref={ref} />

// 第四步：用 load 加载文件，index.js 是必须的
load({
  'index.js': `export default () => 'Hello world!'`,
});
```

## AutoAnimate
https://dev.to/justinschroeder/introducing-autoanimate-add-motion-to-your-apps-with-a-single-line-of-code-34bp

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2g4zgujlrg20fs060wn6.gif)

如上图，添加点动画就可以让 UX 更好，那这种锦上添花的事为啥大部分产品都没有做呢？因为不好接，提升了成本，降低了 UX，尤其是对于新增、被删除和被移动的元素来说。

那有没有「我都要」的办法呢？作者开发的 AutoAnimate 可以很好地解此问题，尺寸仅 1.9Kb。引用到项目中只需增加一两行代码即可实现增加、被删除和被移动元素的列表动画，同时还兼容 React、Vue 以及任何其他框架。

以 React 为例，只需三步。

```js
// 第一步：导入 useAutoAnimate Hook
import { useAutoAnimate } from '@formkit/auto-animate/react';

// 第二部：在组件中使用 useAutoAnimate Hook
const animateParent = useAutoAnimate();

// 第三部：把返回值传给列表的父元素
// 注：你可能会需要 forwardRef 来给子组件传 ref
<ul ref={animateParent} />
```

然后，其子元素就具备了动画能力。

## AntV S2
https://github.com/antvis/s2

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2g5yfhhfqj20w90lswi5.jpg)

S2 是 AntV 团队推出的数据表可视化引擎，旨在提供高性能、易扩展、美观、易用的多维表格。不仅有丰富的分析表格形态，还内置丰富的交互能力, 帮助用户更好地看数和做决策。

S2 是多维交叉分析领域的表格解决方案，数据驱动视图，提供底层核心库、基础组件库、业务场景库，具备自由扩展的能力，让开发者既能开箱即用，也能基于自身场景自由发挥。

S 取自于 SpreadSheet 的两个 S，2 也代表了透视表中的行列两个维度。

## Git ignore .gitignore
https://rubenerd.com/git-ignores-gitignore-with-gitignore-in-gitignore/

程序员冷知识，Git ignore `.gitignore` 会发生什么？

```bash
$ git init .
$ echo ".gitignore" > .gitignore
$ git status
```

。<br />
。<br />
。<br />

答案是 Git 不会忽略这个指令，然后 `.gitignore` 不会被提交。

```
On branch trunk
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

## JS 概念图解
https://blog.bitsrc.io/javascript-under-the-hood-advanced-concepts-developers-should-know-a89ddbb11228

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h2g6w42bmfg20go0a6ahb.gif)

你的代码不是魔法，是有其他人写了另一个程序把他翻译给计算机了。作者用了大量图生动地讲解了 JavaScript 一些高级概念背后是如何工作的，包括作用域链、Hoisting、异步、执行上下文、this 关键字和堆栈等。

还不熟的朋友们可以补补课。

## 周刊一锅端

- [**早早聊的 18 个成长宝藏库**](https://mp.weixin.qq.com/s/3yLbUwqzSy2gFHXkO0PICg)：前端早早鸟，前端早早跑
- [**云谦和他的朋友们**](https://mp.weixin.qq.com/s/NGux3r0P1JJH_z4-vfeksQ)：Umi、Dva 等库作者
- [**DEX 周刊**](https://newsletter.dex.group/)：关于产品、设计、前端、软件的精华资讯邮件列表
- [**前端食堂**](https://mp.weixin.qq.com/s/86Cz3KUWqutu9J0V4tyabQ)：你的前端食堂，吃好每一顿饭

## 小结

如果你喜欢 MDH 前端周刊，请转发给你的朋友，告诉他们[到这里来订阅](https://mp.weixin.qq.com/s?__biz=MjM5NDgyODI4MQ%3D%3D&mid=2247484802&idx=1&sn=caa84339125510680d435a40280a6600)，这是对我最大的帮助。下期见！

<p style="color:#b5495b;">MDH，让开发者有笑容 :)</p>
