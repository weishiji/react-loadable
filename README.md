![React Loadable](http://thejameskyle.com/img/react-loadable-header.png)

> 一个动态导入加载组件的高阶组件.

## 示例

```jsx
import Loadable from 'react-loadable';
import Loading from './my-loading-component';

const LoadableComponent = Loadable({
  loader: () => import('./my-component'),
  loading: Loading,
});

export default class App extends React.Component {
  render() {
    return <LoadableComponent/>;
  }
}
```

## 用户反馈:

- ["我现在非常痴迷于: create-react-app、React Router v4 和 react-loadable的使用. 自由的代码分割, 太简单了."](https://twitter.com/matzatorski/status/872059865350406144)
- ["Webpack 2 upgrade & react-loadable; 花两个小时就可以将初始化加载文件从1.1mb降到529kb."](https://twitter.com/jwbradley87/status/847191118269833216)
- ["Oh hey - 使用 loadable 组件 让我初始化加载文件降到13kB. yeah!轻松搞定"](https://twitter.com/AdamRackis/status/846593080992153600)
- ["太令人惊叹了. 让我主打包文件减少了50kb."](https://github.com/quran/quran.com-frontend/pull/701#issuecomment-287908551)
- ["我使用了 server-side rendering(服务端渲染) + code splitting(代码分割) + PWA ServiceWorker 来缓存数据和资源 😎 (感谢 react-loadable). 现在我的前端程序快到飞起."](https://twitter.com/mxstbr/status/922375575217627136)

## 用户

- [Atlassian](https://www.atlassian.com/)
- [Cloudflare](https://www.cloudflare.com)
- [Curio](https://www.curio.org)
- [Flyhomes](https://flyhomes.com)
- [MediaTek MCS-Lite](https://github.com/MCS-Lite)
- [Spectrum.chat](https://spectrum.chat)
- [Talentpair](https://talentpair.com)
- [Tinder](https://tinder.com/)
- [Unsplash](https://unsplash.com/)

> _ 如果你的公司或者项目也在使用 React Loadable，请加入这个列表(按照字母顺序)

## Also See:

- [`react-loadable-visibility`](https://github.com/stratiformltd/react-loadable-visibility) 和 `react-loadable` 使用相同的API构建, 这个组件能让你动态加载只显示在屏幕上的组件.

<h2>
  <hr>
  <hr>
  <img src="http://thejameskyle.com/img/react-loadable-guide.png" alt="GUIDE">
  <hr>
  <hr>
  <small>Guide</small>
</h2>

所以你现在有了一个React App, 并且你用Webpack去打包你的应用, 所有的事情看起来是那么的顺畅. 但是有一天你突然发现你的文件变得越来越大，打包变得越来越慢。

这时候我们是时候该引入 code-splitting（代码分割）到我们的项目中了!

![A single giant bundle vs multiple smaller bundles](http://thejameskyle.com/img/react-loadable-split-bundles.png)

Code-splitting（代码分割）是把项目中一个大的入口文件分割成多个小的、单独的文件的进程。

这看起来很难，但是一些类似于Webpack的工具已经做到这些了，并且React Loadable是为了让这件事儿变得更加简单。

### Route-based splitting(基于路由的代码风格) vs. Component-based splitting(基于组件的代码分割)

一般我们建议通过不同路由切断你的程序，并且异步加载它们，这对于大多数应用来讲很不错-在浏览器里一个用户点击一个链接并且等待页面加载完成是一个在正常不过的行为。


但是我们可以做的更好。

在React的大多数路由工具中，一个路由就是一个简单的组件,这真没有什么特殊的(Sorry Ryan和Michael-你们是特殊的)。所以我们如果通过组件而不是路由优化分割代码，我们会得到什么呢？


![Route vs. component centric code splitting](http://thejameskyle.com/img/react-loadable-component-splitting.png)

事实证明：相当多。不仅仅是通过路由，还有更多的地方你可以将你的应用程序拆分出来，Modals、tabs还有许多隐藏的UI组件，当用户执行某些操作的时候，你再去加载他们。

> **Example:** 假设你的主应用程序是在一个选项卡里,用户可能永远也不会进入这个选项卡下的应用程序，所以父路由组件为何要加载这个选项卡所对应的组件呢？ 

还有一个地方，你可以优先加载优先级高的组件。那些页面底部的组件为何要和页面顶部的组件同时加载呢？

由于路由即组件，所以我们仍然可以很轻松的在组件层面做code-split(代码分割)

在一个新项目中使用`code-spliting`非常简单，以至于你都不用想两遍，你只需要改动少量代码就可以完成自动的代码分割。

### React Loadable 简介

React Loadable 是一个轻量级的代码分割组件，它简单到令人难以置信。

`Loadable` 是一个告诫组件 (一个创建并返回组件的函数)，它能让你的应用程序在渲染之前动态的加载任何模块

想象有两个组件，一个组件是`import`的组件，另一个是渲染组件

```jsx
import Bar from './components/Bar';

class Foo extends React.Component {
  render() {
    return <Bar/>;
  }
}
```

现在我们通过`import`引入`Bar`这个组件，这是一个同步的引入，但是我们在渲染之前是并不需要这个组件的，所以我们为何不推迟引入这个组件呢？

使用 **dynamic import(动态引入)** ([a tc39 proposal currently at Stage 3](https://github.com/tc39/proposal-dynamic-import))
我们可以修改组件`Bar`使之成为一个异步的。

```js
class MyComponent extends React.Component {
  state = {
    Bar: null
  };

  componentWillMount() {
    import('./components/Bar').then(Bar => {
      this.setState({ Bar });
    });
  }

  render() {
    let {Bar} = this.state;
    if (!Bar) {
      return <div>Loading...</div>;
    } else {
      return <Bar/>;
    };
  }
}
```

但是这是一整个工作流程，并不是单纯的代码分割这一件事儿这么简单，比如当`import()`失败我们该怎么办？怎么作server-side rendering(服务端渲染)？这时候你可以抽象出`Loadable`来解决这些问题。

```js
import Loadable from 'react-loadable';

const LoadableBar = Loadable({
  loader: () => import('./components/Bar'),
  loading() {
    return <div>Loading...</div>
  }
});

class MyComponent extends React.Component {
  render() {
    return <LoadableBar/>;
  }
}
```

### 通过 `import()`自动 code-splitting(代码分割)

在webpack2+中，当你使用`import()`的时候，它会为你[自动代码分割](https://webpack.js.org/guides/code-splitting/)，而不用做外的设置。

这意味着通过使用React Loadable和`import()`可以很快的实验出新的代码分割点来，这是程序中的最佳实践.

### 创建一个更好的"Loading..." 组件
渲染一个静态的"Loading..."已经不能传达出足够的信息给用户了。有时有我们还要想要表现出更多的状态来，比如错误和超时等,这是一个非常好的经历。

```js
function Loading() {
  return <div>Loading...</div>;
}

Loadable({
  loader: () => import('./WillFailToLoad'), // oh no!
  loading: Loading,
});
```
这样做非常好，你的[loading component](#loadingcomponent)会接收多个不同的props。

#### Loading 的错误状态

当[`loader`](optsloader)加载失败，[loading component](#loadingcomponent)组件会接收一个[`error`](propserror)为`true`的prop(否则为`false`).

```js
function Loading(props) {
  if (props.error) {
    return <div>Error!</div>;
  } else {
    return <div>Loading...</div>;
  }
}
```

#### 避免 Loading 组件闪烁的问题

有时候你的组件加载速度非常快(<200ms),Loading组件的loading效果在屏幕上一闪而过.
许多用户反馈，这样的效果会导致用户认为等待的时间要比实际时间还要长，但是如果你什么都不显示，用户反而认为加载很快。

所以你的`loading compoent`将会得到一个布尔类型为`true`[`pastDelay` prop](#propspastdelay)的返回值，当组件的加载时间比设置的[delay](#optsdelay)时间长的时候。

```js
function Loading(props) {
  if (props.error) {
    return <div>Error!</div>;
  } else if (props.pastDelay) {
    return <div>Loading...</div>;
  } else {
    return null;
  }
}
```
默认的`delay`参数为`200ms`，但是你可以根据需要自定义[delay](#optsdelay)这个参数值

```js
Loadable({
  loader: () => import('./components/Bar'),
  loading: Loading,
  delay: 300, // 0.3 seconds
});
```

#### `loader`超时
有时候网络连接断开或者失败，或者永久性挂起，这时候网页无反应，用户不知道是继续等待还是重新刷新页面。这时候当[`loader`](#optsloader)超时后[loading component](#loadingcomponent) 将会接收一个[`timedOut` prop](#propstimedout) 并且这个只值为`true`

```js
function Loading(props) {
  if (props.error) {
    return <div>Error!</div>;
  } else if (props.timedOut) {
    return <div>Taking a long time...</div>;
  } else if (props.pastDelay) {
    return <div>Loading...</div>;
  } else {
    return null;
  }
}
```
然而这个特性默认是被禁止的，如果想打开特性，你可以通过[`timeout` option](#optstimeout)参数传递给 `Loadable`.

```js
Loadable({
  loader: () => import('./components/Bar'),
  loading: Loading,
  timeout: 10000, // 10 seconds
});
```

### 自定义渲染

`Loadable`会渲染`default`导出的组件，如果你想渲染自定义导出的组件，请使用[`render` option](#optsrender)参数

```js
Loadable({
  loader: () => import('./my-component'),
  render(loaded, props) {
    let Component = loaded.namedExport;
    return <Component {...props}/>;
  }
});
```

### 加载多个资源
从技术上讲，你可以用`loader()`加载任何只要是一个promise的资源[you're able to render something](#customizing-rendering)，但是这样用起来确实有点让人恼火。

你可以使用[`Loadable.Map`](#loadablemap)让加载多个资源变得更加容易一些。

```js
Loadable.Map({
  loader: {
    Bar: () => import('./Bar'),
    i18n: () => fetch('./i18n/bar.json').then(res => res.json()),
  },
  render(loaded, props) {
    let Bar = loaded.Bar.default;
    let i18n = loaded.i18n;
    return <Bar {...props} i18n={i18n}/>;
  },
});
```

当使用`Loadable.Map`的时候，[`render()` method](#optsrender)是一个必要参数，他会传递一个匹配后的对象参数到`loader`中去。


### 预加载

还有一个优化项，你可以决定哪些组件在渲染之前进行预先加载。
比如：当按钮点击之前你需要加载一个新的组件，这个组件是被`Loadable`中的[static `preload` method](#loadablecomponentpreload)创建的。

```js
const LoadableBar = Loadable({
  loader: () => import('./Bar'),
  loading: Loading,
});

class MyComponent extends React.Component {
  state = { showBar: false };

  onClick = () => {
    this.setState({ showBar: true });
  };

  onMouseOver = () => {
    LoadableBar.preload();
  };

  render() {
    return (
      <div>
        <button
          onClick={this.onClick}
          onMouseOver={this.onMouseOver}>
          Show Bar
        </button>
        {this.state.showBar && <LoadableBar/>}
      </div>
    )
  }
}
```

<h2>
  <hr>
  <hr>
  <img src="http://thejameskyle.com/img/react-loadable-ssr.png" alt="SERVER SIDE RENDERING">
  <hr>
  <hr>
  <small>Server-Side Rendering(服务端渲染)</small>
</h2>

当你渲染所有所有已经动态加载完成的组件的时候，你将会得到一堆loading的效果，这看起来确实很糟糕，但是好消息是React Loadable在设计之初就支持服务端渲染的，这样就不会出现首屏加载效果了。

我们通过[Express](https://expressjs.com/)开启一个服务。

```js
import express from 'express';
import React from 'react';
import ReactDOMServer from 'react-dom/server';
import App from './components/App';

const app = express();

app.get('/', (req, res) => {
  res.send(`
    <!doctype html>
    <html lang="en">
      <head>...</head>
      <body>
        <div id="app">${ReactDOMServer.renderToString(<App/>)}</div>
        <script src="/dist/main.js"></script>
      </body>
    </html>
  `);
});

app.listen(3000, () => {
  console.log('Running on http://localhost:3000/');
});
```

### 服务端预加载所有的组件

第一件事儿就是在渲染正确的内容之前，确保你的服务端已经加载了所有的组件了。

我们可以使用 [`Loadable.preloadAll`](#loadablepreloadall)
这个方法.他会返回一个所有组件加载完成的一个代理。

```js
Loadable.preloadAll().then(() => {
  app.listen(3000, () => {
    console.log('Running on http://localhost:3000/');
  });
});
```

### 服务端拾起客户端的状态

这可能稍微有一些复杂，这可能会话费我们多一些的精力。
为了能让客户端接管服务端的状态，我们需要在服务端使用相同的代码，
为了实现这一点，我们首先就要通过loadable组件告诉我们到底哪个组件正在渲染。

#### 声明哪个模块被加载
这里有两个参数[`Loadable`](#loadable)和[`Loadable.Map`](#loadablemap)能告诉我们哪个组件正在加载： [`opts.modules`](#optsmodules) 和
[`opts.webpack`](#optswebpack)

```js
Loadable({
  loader: () => import('./Bar'),
  modules: ['./Bar'],
  webpack: () => [require.resolveWeak('./Bar')],
});
```
但是我们不必太担心这些参数，React Loadable有一个[Babel plugin](#babel-plugin)插件可以完成这些设置。

将 `react-loadable/babel` 加入到你的`Babel config`中:

```json
{
  "plugins": [
    "react-loadable/babel"
  ]
}
```
现在这些参数将会被自动被创建。


#### 找出哪些动态模块正在被加载

下一步我们将找到当请求进来的时候，哪些模块是真正需要被加载的。
为此，我们有一个[`Loadable.Capture`](#loadablecapture)组件可以使用，它能收集所有的被加载的模块。

```js
import Loadable from 'react-loadable';

app.get('/', (req, res) => {
  let modules = [];

  let html = ReactDOMServer.renderToString(
    <Loadable.Capture report={moduleName => modules.push(moduleName)}>
      <App/>
    </Loadable.Capture>
  );

  console.log(modules);

  res.send(`...${html}...`);
});
```

#### 将加载的模块映射到打文件上

为了确保客户端加载了所有服务端渲染的模块，我们需要将服务端的模块和webpack打包出来的打包文件做一个映射。

这包含两部分,第一部分我们需要让Webpack告诉我们每个模块需要哪个打包文件，为此我们可以使用[React Loadable Webpack plugin](#webpack-plugin)插件，在webpack config中从`react-loadable/webpack` 引入`ReactLoadablePlugin`插件，传递一个`filename`参数，webpack会将打包文件作为一个JSON数据输出到这个文件中去。

```js
// webpack.config.js
import { ReactLoadablePlugin } from 'react-loadable/webpack';

export default {
  plugins: [
    new ReactLoadablePlugin({
      filename: './dist/react-loadable.json',
    }),
  ],
};
```
然后我们回到我们的服务端，用刚才文件中的数据将模块转换成打包文件的数据
将模块转换成打包文件，需要从`react-loadable/webpack`引入[`getBundles`](#getbundles)方法。
```js
import Loadable from 'react-loadable';
import { getBundles } from 'react-loadable/webpack'
import stats from './dist/react-loadable.json';

app.get('/', (req, res) => {
  let modules = [];

  let html = ReactDOMServer.renderToString(
    <Loadable.Capture report={moduleName => modules.push(moduleName)}>
      <App/>
    </Loadable.Capture>
  );

  let bundles = getBundles(stats, modules);

  // ...
});
```
这时候我们可以通过`<script>`标签渲染这些打包后的文件输出到HTML中。


```js
let bundles = getBundles(stats, modules);

res.send(`
  <!doctype html>
  <html lang="en">
    <head>...</head>
    <body>
      <div id="app">${html}</div>
      <script src="/dist/main.js"></script>
      ${bundles.map(bundle => {
        return `<script src="/dist/${bundle.file}"></script>`
      }).join('\n')}
    </body>
  </html>
`);
```

#### 客户端会等待所有的打包文件加载完成

因为Webpack的工作方式是，我们的主打包文件会比其他的scripts预先加载，所以我们需要等待所有的文件加载完成后才开始渲染。
为此我们需要一个全局的函数供我们调用当所有的打包文件被加载后，我们将在客户端使用[`Loadable.preloadReady()`](#loadablepreloadready)这个方法，就像在服务器使用[`Loadable.preloadAll()`](#loadablepreloadall)这个方法一样。


```js
// src/entry.js
import React from 'react';
import ReactDOM from 'react-dom';
import Loadable from 'react-loadable';
import App from './components/App';

window.main = () => {
  Loadable.preloadReady().then(() => {
    ReactDOM.hydrate(<App/>, document.getElementById('app'));
  });
};
```
这时候在我们服务端返回的HTML的末尾处的 `<script>`标签中调用那个全局函数。

```js
let bundles = getBundles(stats, modules);

res.send(`
      ...
      <script src="/dist/main.js"></script>
      ${bundles.map(...).join('\n')}
      <script>window.main();</script>
    </body>
  </html>
`);
```

<h4 align="center">
  Now server-side rendering should work perfectly!
</h4>

<h2>
  <hr>
  <hr>
  <img src="http://thejameskyle.com/img/react-loadable-api-docs.png" alt="API DOCS">
  <hr>
  <hr>
  <small>API 文档</small>
</h2>

### `Loadable`

[rendering](#optsrender)前动态[loading](#optsloader)模块的一个高阶组件，当模块无法被加载的时候，[loading](#opts.loading) 组件会被渲染.

```js
const LoadableComponent = Loadable({
  loader: () => import('./Bar'),
  loading: Loading,
  delay: 200,
  timeout: 10000,
});
```

它返回一个 [LoadableComponent](#loadablecomponent)组件.

### `Loadable.Map`

一个允许你并行加载多个资源点的高阶组件。
Loadable.Map's [`opts.loader`](#optsloader)接收一个对象，并且需要[`opts.render`](#optsrender)方法

```js
Loadable.Map({
  loader: {
    Bar: () => import('./Bar'),
    i18n: () => fetch('./i18n/bar.json').then(res => res.json()),
  },
  render(loaded, props) {
    let Bar = loaded.Bar.default;
    let i18n = loaded.i18n;
    return <Bar {...props} i18n={i18n}/>;
  }
});
```
当调用`Loadable.Map`中的`render()`方法， 这个方法中的`loaded`参数将会和`loader`方法起到一样的作用。

### `Loadable` and `Loadable.Map` Options

#### `opts.loader`

一个加载模块的promose函数


```js
Loadable({
  loader: () => import('./Bar'),
});
```
当调用[`Loadable.Map`](#loadablemap)的时候，它接收对象行函数。

```js
Loadable.Map({
  loader: {
    Bar: () => import('./Bar'),
    i18n: () => fetch('./i18n/bar.json').then(res => res.json()),
  },
});
```
当调用`Loadable.Map`的时候，你也需要传递[`opts.render`](#optsrender)函数

#### `opts.loading`

当模块加载或者加载失败的时候，这个组件会被渲染。

```js
Loadable({
  loading: LoadingComponent,
});
```
这个参数是必选参数，如果你不想渲染任何，让它返回`null`就好了。

```js
Loadable({
  loading: () => null,
});
```

#### `opts.delay`

延时毫秒数[`props.pastDelay`](#propspastdelay)后加载渲染[`loading`](#optsloading)组件，默认值是`200`

```js
Loadable({
  delay: 200
});
```

[查看关于更多delay](#avoiding-flash-of-loading-component).

#### `opts.timeout`

[`props.timedOut`](#propstimedout)超时的毫秒数后显示[`loading`](#optsloading)组件，默认是关闭的。

```js
Loadable({
  timeout: 10000
});
```

[更多关于 timeouts](#timing-out-when-the-loader-is-taking-too-long).

#### `opts.render`
自定义渲染加载模块的函数
它接收 [`opts.loader`](#optsloader)代理返回的`loaded`参数和[`LoadableComponent`](#loadablecomponent)传递的`props`两个参数。

```js
Loadable({
  render(loaded, props) {
    let Component = loaded.default;
    return <Component {...props}/>;
  }
});
```

#### `opts.webpack`
可选参数，可通过`require.resolveWeak`获取返回的一个Webpack模块id的集合。

```js
Loadable({
  loader: () => import('./Foo'),
  webpack: () => [require.resolveWeak('./Foo')],
});
```
这个参数可通过[Babel Plugin](#babel-plugin)自动生成.

#### `opts.modules`

可选参数，imports模块路径的数组集合

```js
Loadable({
  loader: () => import('./my-component'),
  modules: ['./my-component'],
});
```

可选参数，可通过[Babel Plugin](#babel-plugin)插件自动生成。

### `LoadableComponent`

`Loadable` 和 `Loadable.Map`返回的组件.

```js
const LoadableComponent = Loadable({
  // ...
});
```
当组件加载的时候调用[`opts.render`](#optsrender)方法，它会直接接收props参数。

#### `LoadableComponent.preload()`

[`LoadableComponent`](#loadablecomponent)调用的一个静态方法，可以让组件预加载。

```js
const LoadableComponent = Loadable({...});

LoadableComponent.preload();
```
返回一个代理，但是尽量避免此代理阻塞你的UI更新，否则会带来非常不好的用户体验。

[关于更多 preloading](#preloading).

### `LoadingComponent`
 传给[`opts.loading`](#optsloading)方法的一个组件.

```js
function LoadingComponent(props) {
  if (props.error) {
    // When the loader has errored
    return <div>Error!</div>;
  } else if (props.timedOut) {
    // When the loader has taken longer than the timeout
    return <div>Taking a long time...</div>;
  } else if (props.pastDelay) {
    // When the loader has taken longer than the delay
    return <div>Loading...</div>;
  } else {
    // When the loader has just started
    return null;
  }
}

Loading({
  loading: LoadingComponent,
});
```

[关于更多 loading components](#creating-a-great-loading-component)

#### `props.error`

[`LoadingComponent`](#loadingcomponent)的一个布尔类型的参数，当[`loader`](#optsloader)模块加载失败的时候，为`true`.

```js
function LoadingComponent(props) {
  if (props.error) {
    return <div>Error!</div>;
  } else {
    return <div>Loading...</div>;
  }
}
```

[更多关于 errors](#loading-error-states).

#### `props.timedOut`

[`LoadingComponent`](#loadingcomponent)组件设置[`timeout`](#optstimeout)参数后,`props.timedOut`将接受一个布尔类型的返回值。

```js
function LoadingComponent(props) {
  if (props.timedOut) {
    return <div>Taking a long time...</div>;
  } else {
    return <div>Loading...</div>;
  }
}
```

[更多关于 timeouts](#timing-out-when-the-loader-is-taking-too-long).

#### `props.pastDelay`

[`LoadingComponent`](#loadingcomponent)设置[`delay`](#optsdelay)参数后，`props.pastDelay`将接受一个布尔类型的返回值.

```js
function LoadingComponent(props) {
  if (props.pastDelay) {
    return <div>Loading...</div>;
  } else {
    return null;
  }
}
```

[更多关于delays](#avoiding-flash-of-loading-component).

### `Loadable.preloadAll()`

等待所有被预加载的组件[`LoadableComponent.preload`](#loadablecomponentpreload)完成加载，允许你在各种环境里预加载你的组件，比如在服务端。

```js
Loadable.preloadAll().then(() => {
  app.listen(3000, () => {
    console.log('Running on http://localhost:3000/');
  });
});
```

注意非常重要的一点，预先加载你所有声明过的`loadable`组件在启动你的服务.

**Good:**

```js
// During module initialization...
const LoadableComponent = Loadable({...});

class MyComponent extends React.Component {
  componentDidMount() {
    // ...
  }
}
```

**Bad:**

```js
// ...

class MyComponent extends React.Component {
  componentDidMount() {
    // During app render...
    const LoadableComponent = Loadable({...});
  }
}
```

> **注意:** `Loadable.preloadAll()` 如果你的项目里有多个`Loadable.preloadAll()`,`react-loadable`将会失效。

[更多关于 preloading on the server](#preloading-all-your-loadable-components-on-the-server).

### `Loadable.preloadReady()`

检查浏览器里已经加载所有模块并且调用`matching`[`LoadableComponent.preload`](#loadablecomponentpreload)方法

```js
window.main = () => {
  Loadable.preloadReady().then(() => {
    ReactDOM.hydrate(<App/>, document.getElementById('app'));
  });
};
```

[更多关于 preloading on the client](#waiting-to-render-on-the-client-until-all-the-bundles-are-loaded).

### `Loadable.Capture`

记录哪个模块被渲染的一个组件.

每个被React Loadable组件在被渲染的时候，接收一个被每个`moduleName`调用的`report` prop。

```js
let modules = [];

let html = ReactDOMServer.renderToString(
  <Loadable.Capture report={moduleName => modules.push(moduleName)}>
    <App/>
  </Loadable.Capture>
);

console.log(modules);
```

[更多关于 capturing rendered modules](#finding-out-which-dynamic-modules-were-rendered).

## Babel Plugin

为每个loadable 组件适配[`opts.webpack`](#optswebpack)和[`opts.modules`](#optsmodules)参数是一件很耗费体力的一件事儿，并且你还要始终记着去做。

你可以用Babel plugin写到你的配置文件里去让Webpack自动完成这件事儿，从而代替手动去做。

```json
{
  "plugins": ["react-loadable/babel"]
}
```

**Input**

```js
import Loadable from 'react-loadable';

const LoadableMyComponent = Loadable({
  loader: () => import('./MyComponent'),
});

const LoadableComponents = Loadable.Map({
  loader: {
    One: () => import('./One'),
    Two: () => import('./Two'),
  },
});
```

**Output**

```js
import Loadable from 'react-loadable';
import path from 'path';

const LoadableMyComponent = Loadable({
  loader: () => import('./MyComponent'),
  webpack: () => [require.resolveWeak('./MyComponent')],
  modules: [path.join(__dirname, './MyComponent')],
});

const LoadableComponents = Loadable.Map({
  loader: {
    One: () => import('./One'),
    Two: () => import('./Two'),
  },
  webpack: () => [require.resolveWeak('./One'), require.resolveWeak('./Two')],
  modules: [path.join(__dirname, './One'), path.join(__dirname, './Two')],
});
```

[更多关于declaring modules](#declaring-which-modules-are-being-loaded).

## Webpack Plugin

当服务端渲染的时候，为了[send the right bundles down](#mapping-loaded-modules-to-bundles)，你需要`React Loadable Webpack plugin`插件生成一个模块和打包文件有对应关系的JSON文件.

```js
// webpack.config.js
import { ReactLoadablePlugin } from 'react-loadable/webpack';

export default {
  plugins: [
    new ReactLoadablePlugin({
      filename: './dist/react-loadable.json',
    }),
  ],
};
```

它将产生一个JSON文件(`opts.filename`)，你可以引入这个模块和打包文件相对应的JSON文件

[更多关于 mapping modules to bundles](#mapping-loaded-modules-to-bundles).

### `getBundles`

通过`react-loadable/webpack`方法可以导出一个模块和打包文件的映射关系.

```js
import { getBundles } from 'react-loadable/webpack';

let bundles = getBundles(stats, modules);
```

[更多关于 mapping modules to bundles](#mapping-loaded-modules-to-bundles).

<h2>
  <hr>
  <hr>
  <img src="http://thejameskyle.com/img/react-loadable-faq.png" alt="FAQ">
  <hr>
  <hr>
  <small>FAQ</small>
</h2>

### 如果避免重复调用?

假定你的`Loadable()`会重复设置`loading`组件和`delay`参数,你可以用高阶组件 (HOC)去封装一层`Loadable`,并为它设置一些默认参数.

```js
import Loadable from 'react-loadable';
import Loading from './my-loading-component';

export default function MyLoadable(opts) {
  return Loadable(Object.assign({
    loading: Loading,
    delay: 200,
    timeout: 10,
  }, opts));
};
```

这时候你只需要设置`loader`就可以使用它。

```js
import MyLoadable from './MyLoadable';

const LoadableMyComponent = MyLoadable({
  loader: () => import('./MyComponent'),
});

export default class App extends React.Component {
  render() {
    return <LoadableMyComponent/>;
  }
}
```

不幸的是，如果你用HOC对Loadable封装一层会使[react-loadable/babel](#babel-plugin)失效，所以这时候你需要手动的添加必要参数（`modules`, `webpack`）.

```js
import MyLoadable from './MyLoadable';

const LoadableMyComponent = MyLoadable({
  loader: () => import('./MyComponent'),
  modules: ['./MyComponent'],
  webpack: () => [require.resolveWeak('./MyComponent')],
});

export default class App extends React.Component {
  render() {
    return <LoadableMyComponent/>;
  }
}
```

### 如果在服务端渲染中使用其他的比如`.css`或者`.map`资源

当你调用[`getBundles`](#getbundles)方法的时候，它会返回Javascript依赖的文件类型在你的Webpack配置中.

为得到这些，你需要手动的过滤一下文件类型,像这样：

```jsx
let bundles = getBundles(stats, modules);

let styles = bundles.filter(bundle => bundle.file.endsWith('.css'));
let scripts = bundles.filter(bundle => bundle.file.endsWith('.js'));

res.send(`
  <!doctype html>
  <html lang="en">
    <head>
      ...
      ${styles.map(style => {
        return `<link href="/dist/${style.file}" rel="stylesheet"/>`
      }).join('\n')}
    </head>
    <body>
      <div id="app">${html}</div>
      <script src="/dist/main.js"></script>
      ${scripts.map(script => {
        return `<script src="/dist/${script.file}"></script>`
      }).join('\n')}
    </body>
  </html>
`);
```
