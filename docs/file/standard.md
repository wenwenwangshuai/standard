# 目录规范

### 1. 文件夹和文件夹内部文件的语义一致性

模块文件夹应该保证单一入口和出口

```
├── config
│   ├── config.ts
│   └── routes.ts
├── src
│   ├── assets // 资源文件
│   │   └── img
│   │       └── logo.png
│   ├── components
│   │   └── input
│   │       ├── index.tsx
│   │       └── index.module.less
│   ├── config // 配置信息
│   │   └── index.ts
│   ├── constants // 常量配置
│   │   ├── common.ts
│   │   ├── base.ts
│   │   └── index.ts // 暴露变量的出口
│   ├── type // ts类型定义
│   │   ├── seller
│   │   │   └── index.ts
│   │   ├── buyer
│   │   │   └── index.ts
│   │   └── common.ts
│   ├── layouts // page容器组件
│   │   └── BasicLayout
│   │       ├── index.tsx
│   │       └── index.module.less
│   ├── pages // 页面视图层
│   │   ├── seller
│   │   │   ├── components
│   │   │   │   └── input
│   │   │   │       ├── index.tsx
│   │   │   │       └── index.module.less
│   │   │   ├── utils
│   │   │   │   └── index.ts
│   │   │   ├── index.tsx
│   │   │   └── index.module.less
│   │   ├── buyer
│   │   │   ├── components
│   │   │   │   └── input
│   │   │   │       ├── index.tsx
│   │   │   │       └── index.module.less
│   │   │   ├── utils
│   │   │   │   └── index.ts
│   │   │   ├── index.tsx
│   │   │   └── index.module.less
│   │   ├── 404.tsx
│   │   └── document.ejs
│   ├── service // api层
│   │   ├── seller
│   │   │   └── index.ts
│   │   ├── buyer
│   │   │   └── index.ts
│   │   └── common.ts
│   │── utils
│   │   ├── request.ts  // axios拦截
│   │   ├── tips.ts     // 各种tip提示统一封装类
│   │   ├── utils.ts    // 工具集合
│   │   └── validate.ts // 常见正则校验
│   │── app.ts
│   └── loading.tsx
├── tsconfig.json
├── CHANGELOG.md
├── README.md
└── package.json
```

一般来说我们的目录结构都会有一个文件夹是按照路由模块来划分的，这个文件夹一般叫 `pages` 。这个文件夹里面应该包含我们项目所有的路由模块（上面的例子路由模块指的是 `seller` 和 `buyer`），并且仅应该包含路由模块，而不应该有别的其他的非路由模块的文件夹。

这样做的好处在于一眼从 `pages` 文件夹看出这个项目的路由，比如上面的例子我们就可以说有 `/seller` 和 `/buyer` 两个路由，如果混入了其他非路由的文件夹就会混淆这里面都是路由文件夹的约定。

### 2. 单一入口/出口

接着上面的例子来讲，`constants` 文件夹应该作为一个独立的模块由外部引入，并且 `constants/index.ts` 应该作为外部引入 `constants` 模块的唯一入口。换句话说，假如我有个页面要引入 `constants` 的 `base`，我应该从 `constants/index.ts` 文件引入，而不应该直接从 `constants/base.ts` 引入。因为外部不应该知晓文件夹内部的文件分布，而是应该全部从 `index.ts` 导入。同时 `constants` 内部文件不管如何分布，需要外部引入的都要在入口文件 `index.ts` 导出。

```js
// 错误用法
import base from '@/constants/base'

// 正确用法
import { base } from '@/constants'

// constants/index.ts
// 同时
import base from './base'
export {
    base
}
```

这样做的好处在于，无论你的模块文件夹内部有多乱，外部引用的时候，都是从一个入口文件引入，这样就很好的实现了隔离，如果后续有重构需求，你就会发现这种方式的优点

### 3. 就近原则，紧耦合的文件应该放到一起，且应以相对路径引用

继续使用上面的例子，在 `seller/index.tsx` 中使用样式，一般我们会用相对路径的方式。这里为什么不用绝对路径，可能有人说是写起来方便，但是实际上这里用相对路径可以保证这个 `seller` 模块内部的独立性。

```js
// seller/index.tsx
// 正确用法
import styles from './index.module.less'
// 错误用法
import styles from '@/pages/seller/index.module.less'
```

怎样理解？我们现在的 `seller` 目录是在 `src/pages/seller`，如果我们后续发生了路由变更，需要加一个层级，变成 `src/pages/user/seller`。如果我们采用第一种相对路径的方式，那就可以直接将整个文件夹拖过去就好，`seller` 文件夹内部不需要做任何变更。但是如果我们采用第二种绝对路径的方式，移动文件夹的同时，还需要对每个 `import` 的路径做修改。

这样做的好处？就是上面说的。

### 4. 公共的文件应该以绝对路径的方式从根目录引用

公共指的是多个路由模块共用，上面的 `src/components/input` 就是一个公共组件 。如果我们要在 `buyer` 模块使用这个组件可能有下面两种引用方式

```js
// buyer/index.tsx
// 错误用法
import Input from '../../components/input'
// 正确用法
import Input from '@/components/input'
```

为什么？和上面的原因一样，如果我们需要对文件夹结构进行调整。将 `/src/components/input` 变成 `/src/components/new/input`，如果使用绝对路径，只需要全局搜索替换。而如果使用相对路径，则需要一个个找，很麻烦。

### 5. /src 外的文件不应该被引入

这一点就比较好理解了，而且其实已经有脚手架做了相关的约束了，正常我们的前端项目都会有个 src 文件夹，里面放着所有的项目需要的资源，js, css, png, svg 等等。src 外会放一些项目配置，依赖，环境等文件。所以，src 文件夹外不应该放需要被引入的资源。

这样的好处是方便划分项目代码文件和配置文件

**总结**

项目的目录结构很重要，因为目录结构能体现很多东西，怎么规划目录结构可能每个人有自己的理解。但是上面这些原则是我个人觉得是比较通用的，如果一个项目能遵循上面的原则，至少可以说，不会乱。

这里有两个点我觉得是需要拎出来再强调一遍的：

* 一个是**约定带来的语义**，上面的 5 点都体现了这点，约定带来的语义可以很大程度减少我们理解项目的时间和难度
  
* 另一个就是**面向重构编程**，其实无论是使用 TypeScript 还是像上面的 3，4点一样约定对模块文件和公用文件的引用方式，都是为了在项目结构调整或者重构的时候，更快更安全的对文件进行修改。一个成熟的长期维护的项目，是一定要把重构的坑埋到代码里面的。