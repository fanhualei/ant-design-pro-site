---
order: 6
title: 修改样式
type: 开发
---




这篇文档主要针对如何在项目中使用和规划样式文件进行介绍，如果想获取基础的 CSS 知识或查阅属性，可以参考 [MDN 文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference)。

## 相关知识

### less

Ant Design Pro 默认使用 less 作为样式语言，建议在使用前或者遇到疑问时学习一下 [less](http://lesscss.org/) 的相关特性。

### CSS Modules

在样式开发过程中，有两个问题比较突出：

- 全局污染 —— CSS 文件中的选择器是全局生效的，不同文件中的同名选择器，根据 build 后生成文件中的先后顺序，后面的样式会将前面的覆盖；
- 选择器复杂 —— 为了避免上面的问题，我们在编写样式的时候不得不小心翼翼，类名里会带上限制范围的标识，变得越来越长，多人开发时还很容易导致命名风格混乱，一个元素上使用的选择器个数也可能越来越多。

为了解决上述问题，我们的脚手架默认使用 CSS Modules 模块化方案。CSS Modules 的基本原理很简单，就是对每个类名（非 :global 声明的）按照一定规则进行转换，保证它的唯一性。如果在浏览器里查看这个示例的 dom 结构，你会发现实际渲染出来是这样的：

```html
<div class="title___3TqAx">title</div>
```

类名被自动添加了一个 hash 值，这保证了它的唯一性。

除了上面的基础知识，还有一些关键点需要注意：

- CSS Modules 只会对 `className` 以及 `id` 进行转换，其他的比如属性选择器，标签选择器都不进行处理，推荐尽量使用 className。
- 由于不用担心类名重复，你的 className 可以在基本语意化的前提下尽量简单一点儿。

上面只对 CSS Modules 进行了最基础的介绍，有兴趣可以参考其他文档：

- [github/css-modules](https://github.com/css-modules/css-modules)
- [CSS Modules 用法教程](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)
- [CSS Modules 详解及 React 中实践](https://github.com/camsong/blog/issues/5)


## 定义样式



### 定义全局样式

全局样式文件`src/global.less`，在这里你可以进行一些通用设置，比如脚手架中自带的：

```css
.gtitle {
  color: red;
}
```

在页面文件中直接引用就可以了。
```tsx
<div className="gtitle">在global.less中定义</div>
```


> 因为 antd 中会自带一些全局设置，如字号，颜色，行高等，所以在这里，你只需要关注自己的个性化需求即可，不用进行大量的 reset。

### src/utils/utils.less

这里可以放置一些工具函数供调用，比如清除浮动 `.clearfix`。

### 定义模块样式

针对某个模块/页面生效的文件。

#### 通用模块级

例如 `src/layouts/BasicLayout.less`，里面包含一些基本布局的样式，被 `src/layouts/BasicLayout.ts` 引用，项目中使用这种布局的页面就不需要再关心整体布局的设置。如果你的项目中需要使用其他布局，也建议将布局相关的 js 和 less 放在这里 `src/layouts`。

#### 页面级

具体页面相关的样式，例如 `src/routes/Dashborad/Monitor.less`，里面的内容仅和本页面的内容相关。一般情况下，如果不是页面内容特别复杂，有了前面全局样式、通用模块样式的配合，这里要写的应该不多。

#### 组件级

这个也很简单，就是组件相关的样式了，有一些在页面中重复使用的片段或相对独立的功能，你可以提炼成组件，相关的样式也应该提炼出来放在组件中，而不是混淆在页面里。

> 以上样式类别都是针对独立的样式文件，有时样式配置特别简单，也没有重复使用，你也可以用内联样式 `style={{ ... }}` 来设置。

## 覆盖样式

### 局部覆盖

由于业务的个性化需求，我们经常会遇到需要覆盖组件样式的情况，这里举个简单的例子。

例如希望修改Button组件的背景颜色，为了避免全局污染，可以在页面自己的less文件中定义一个CSS样式

```less
.customButton {
  background-color: pink;
}
```

然后在页面中直接引用

```tsx
import React from 'react';
import { Button } from 'antd';
import styles from './index.less';

export default () => {
  return (
    <>
      <Button className={styles.customButton}> 覆盖Ant Button样式 </Button>
      <Button> 没有覆盖Ant Button样式 </Button>
    </>
  );
};
```

### 全局覆盖

方法一：主题定制。 例如覆盖antDesign样式，[具体见antDesign文档](https://ant.design/docs/react/customize-theme-cn)

方法二：global.less直接定义就可以了，例如覆盖Button的背景颜色:

```less
.ant-btn {
  background-color: red;
}
```

> 这里有一个注意事项，如果在global中直接覆盖后，global的优先级比较高，还想在页面中再次覆盖，那么需要添加 `!important` 提高优先级 



