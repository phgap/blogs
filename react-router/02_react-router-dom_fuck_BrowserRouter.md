# React-Router

今天，我们一起来学习React-Router的源码。下面是我跟小S同学学习源码的过程。
大家可以参照这个思路，进行源码的学习。

## Router
**我:**
小S，今天我们来一起学习React-router-dom的源码吧
>:expressionless::
>好呀！

**我:**
首先，react-router的官网上，有基本的使用方法。
[这里](https://reacttraining.com/react-router/web/guides/basic-components)
 列出了常用的组件，以及它们的用法
1. Router
2. Route
3. Switch
4. Link

>:expressionless::
>好的， 继续

**我:**
先从这些组件的源码入手，那肯定第一个就是BrowserRouter，或者HashRouter

>:expressionless::
>那应该怎么入手呢？

**我:**
首先，从[github](https://github.com/ReactTraining/react-router)上，得到与文档版本对应的代码。<br>
**我:**
接着看路径结构。是这样的：

![](https://github.com/phgap/blogs/blob/master/react-router/imgs/00_project_structure.png)

>:expressionless::
>接下来我一般就是找教程先简单过一遍，代码下下来然后把node__modules复制出来debugger
>然后看不懂了就放弃

**我:**
不，你进入细节之前，要先搞清楚结构

>:expressionless::
>恩啊， 不然怎么找代码

**我:**
你看到这个路径之后，第一步，应该猜一猜，这些文件夹都是干啥的，哪个是你需要的

>:expressionless::
>script是build, website是doc, packges是功能<br>
>:expressionless::
>这个都差不多

**我:**
对。打开各个文件夹，会发现，packages里面的东西，是我们想要的源码。
#### todo图
![01_react-router-packages-structure.png]()
我们肯定先从源码看起，因为这次读源码首先要学习的是实现原理，并不是如何构建<br>
**我:**
那咱们就从[react-router-dom](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-dom)开始呗
**我:**
打开react-router-dom,奔着module去

>:expressionless::
>直接从github上下载master的分支么

**我:**
嗯

>:expressionless::
>为啥看module

**我:**
docs、tools、都是附属的啊
>:expressionless::
>不应该先看package。json和rollup么

**我:**
核心代码，肯定是在module里了。我要先看看整个的结构，有个大致的印象

>:expressionless::
>恩恩

**我:**
打开modules就看到了我们刚刚文档中提及的几个组件了<br>
**我:**
我们先从[BrowserRouter.js](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/modules/BrowserRouter.js)入手

>:expressionless::
>嗯哼

**我:**
那我要打开这个文件，开始看代码了<br>
**我:**
我先不关注package.json这些配置文件
>:expressionless::
>残暴

**我:**
因为我这次是要看原理，不是看整个源码如何build<br>
**我:**
配置文件也是辅助而已

>:expressionless::
>嗯啊。<br>
>:expressionless::
>可是有时候还是很重要的

**我:**
那就用到了再说

>:expressionless::
>是不是至少看一下都用了什么和几个入口

**我:**
用到了什么也不需要在package.json中看，因为我关注的那几个组件，用到啥会import的。所以看源码，最重要的是focus on。你要有关注点，因为有的源码，是非常庞大的。一不小心就掉进了细节的海洋出不来了。

>:expressionless::
>有道理<br>
>:expressionless::
>比如react

**我:**
对，你不可能一次就读懂他里面的东西，所以你要看很多次<br>
**我:**
每次的关注点可以不同

>:expressionless::
>恩啊<br>
>:expressionless::
>确实如此

**我:**
都揉到一起，会觉得非常乱，最后就放弃了

**我:**
而且，我们学习源码，也不一定要把源码中的每个特性都在同一个项目中都用到，还是要分开学，分开用

>:expressionless::
>有道理<br>
>:expressionless::
>我就总忍不住乱看

**我:**
那就先看[BrowserRouter.js](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/modules/BrowserRouter.js)了。<br>
**我:**
打开文件，看了一下，挺开心，代码没几行

>:expressionless::
>然后一脸懵逼记不住， 看不懂

**我:**
哈哈，代码这么少，那肯定是有依赖组件了<br>
**我:**
先看看依赖了哪些组件<br>
**我:**
我这个源码，应该是旧版本的，跟你下载的不太一样，我待会下载一个新版本。

>:expressionless::
>旧的好<br>
>:expressionless::
>真的

**我:**
不，我觉得新的好，旧的context使用起来非常不方便<br>
**我:**
新的，用Provider组件和Comsumer组件给封装了<br>

**我:**
回到[BrowserRouter.js](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/modules/BrowserRouter.js)。从依赖看，我最感兴趣的是 history和react-router。如下：
```jsx
import React from "react";
import { Router } from "react-router";
import { createBrowserHistory as createHistory } from "history";
import PropTypes from "prop-types";
import warning from "tiny-warning";
```
>:expressionless::
>history是库啊<br>
>:expressionless::
>等等<br>
>:expressionless::
>我有点没跟上<br>

**我:**
等待了30秒......<br>
为啥我感兴趣这俩呢

>:expressionless::
>你的兴趣点对<br>
>:expressionless::
>我以前看过源码相关教程， 了解一点history<br>

**我:**
嗯。官网说了啊。如下：
>#### Routers
>At the core of every React Router application should be a router component. For web projects, react-router-dom provides <BrowserRouter> and <HashRouter> routers. Both of these will create a specialized ***history*** object for you.

**我:**
他实现路由的时候，肯定是用到这个了的<br>
**我:**
所以，这个可能会作为读源码的预备知识。<br>
**我:**
但是我先不管他，看看影响react-router的阅读不<br>
**我:**
另外，之前说过，这个文件源码行数很少，肯定依赖了其他的组件。看起来，就这个react-router担当了重要责任。<br>
**我:**
所以现在有两个todo:**history**和**react-router**

>:expressionless::
>嗯

**我:**
那一会需要关注的就是react-router这个包了<br>
**我:**
我先不管刚才的两个todo，我把这个组件([BrowserRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/modules/BrowserRouter.js))先看看，反正代码又不多<br>
```jsx
class BrowserRouter extends React.Component {
  history = createHistory(this.props);

  render() {
    return <Router history={this.history} children={this.props.children} />;
  }
}

if (__DEV__) {
  BrowserRouter.propTypes = {
    basename: PropTypes.string,
    children: PropTypes.node,
    forceRefresh: PropTypes.bool,
    getUserConfirmation: PropTypes.func,
    keyLength: PropTypes.number
  };

  BrowserRouter.prototype.componentDidMount = function() {
    warning(
      !this.props.history,
      "<BrowserRouter> ignores the history prop. To use a custom history, " +
        "use `import { Router }` instead of `import { BrowserRouter as Router }`."
    );
  };
}
```
**我:**
我要把if(__DEV__)的分支删掉，因为我现在要看的是最最核心的东西<br>
**我:**
那就俩东西了
```jsx
//......
class BrowserRouter extends React.Component {
  history = createHistory(this.props);

  render() {
    return <Router history={this.history} children={this.props.children} />;
  }
}
//......
```

**我:**
切记过早的进入__DEV__,那个是方便开发用的，通常与核心的概念关系不大<br>


**我:**
所以现在BrowserRouter的任务，就是创建一个history对象，传给react-router的Router

>:expressionless::
>这个时候

**我:**
嗯，你说

>:expressionless::
>你会选择看router还是createHistory

**我:**
哈哈，这个时候，我其实想看一眼hashRouter

>:expressionless::
>我也是

**我:**
因为import的一句话
```jsx
import { createBrowserHistory as createHistory } from "history";
```
所以我有理由怀疑，代码类似，就是导入了不同的函数

**HashRouter.js**
```jsx
import React from "react";
import { Router } from "react-router";
import { createHashHistory as createHistory } from "history";
import PropTypes from "prop-types";
import warning from "tiny-warning";
```
还真是

**我:**
那我就把关注点，放在router上了
**我:**
因为
1. history我猜出他是干啥了，跟浏览器路径有关
2. router里面用到它了，我等用到了，不知道做什么的时候，在去看history，这样行不行

>:expressionless::
>恩啊

**我:**
回到这个路径

#### todo 图

**我:**
去看react-router

>:expressionless::
>为什么是他

**我:**
因为它导入包时，没加相对路径啊<br>
```jsx
import { Router } from "react-router";
```
**我:**
我就往上翻一翻呗，当然，估计在配置文件中，应该会有相关配置

>:expressionless::
>恩恩

**我:**
进这个路径，文件真tmd多，mmp的<br>
**我:**
导入的是一个包，包下有index.js，我是不是应该先看这个js

>:expressionless::
>我是这个习惯，先看index是不是只做了import

**我:**
但是其实我们在看recat-router-dom的时候，网上会有一些与react-router的比较的讨论，

>:expressionless::
>没太注意<br>
>:expressionless::
>稀里糊涂

**我:**
所以，react-router是一个包，我是之前就知道的，但是，我并不确定他的代码在哪，如果找不到，我可能会从github上其他的位置找，或者从npm的官网找链接了

>:expressionless::
>恩啊

**我:**
进index.js吧<br>
**我:**
代码不多，分成production和else分支<br>
**我:**
我会选择else分支<br>
**我:**
但是发现一个问题啊，我艹<br>
**我:**
当前路径下，没有cjs文件夹<br>
**我:**
因为BrowserRouter导入的是一个包<br>
**我:**
所以这个包，得是build之后的

>:expressionless::
>这个时候就要看packge的script了

**我:**
嗯，可以的<br>
**我:**
不过我感觉略微跑偏了<br>
**我:**
我要回到router本身上<br>

>:expressionless::
>好好<br>
>:expressionless::
>继续<br>
>:expressionless::
>怎么回到router本身

**我:**
/react-router下，有一个router.js文件<br>
**我:**
打开看，只有那两行代码，不是我要的东西啊<br>
**我:**
它导出的，还是index.js编译之后的

>:expressionless::
>看modules

**我:**
对，看modules<br>
**我:**
打开modules下的Router.js

>:expressionless::
>要是我的话， 这个时候就跑偏了<br>
>:expressionless::
>直接去看rollup了<br>
>:expressionless::
>然后最后找到router<br>
>:expressionless::
>router.js

**我:**
我也可能会跑偏<br>
**我:**
我之前就跑到history上去了<br>
**我:**
但是后来想想，这样不太好<br>
**我:**
从看源码角度说，直接找到modules下的Router.js很容易<br>
**我:**
因为其他文件，一看就不是源码实现<br>

>:expressionless::
>嗯啊

**我:**
现在打开它，一看，挺像啊，那先看看有多少行<br>
**我:**
百十来行，有信心了，哈哈

>:expressionless::
>然后这么少的代码<br>
>:expressionless::
>第一反应看一下引入

**我:**
对<br>
**我:**
但是你看，一个五个<br>

>:expressionless::
>前三个忽略,一看就没用

**我:**
是的<br>
**我:**
我现在其实有点关注第五个了

>:expressionless::
>我会看render

**我:**
先不着急<br>
**我:**
因为如果他的名字叫做warn<br>
**我:**
是警告的意思

>:expressionless::
>恩恩<br>
>:expressionless::
>搜一下

**我:**
警告通常都是开发版的东西，如果能排除，那就剩第四个依赖了

>:expressionless::
>可能没用<br>
>:expressionless::
>再看dev里面的

**我:**
对，当前文件搜索了一下，在__DEV__分支下，不看了，哈哈<br>
**我:**
那就剩一个context.js了呗

>:expressionless::
>过分

**我:**
我觉得我现在想扫一眼这个文件，如果内容不多，我就搞定他，如果多的话，那就先放那

>:expressionless::
>恩恩

**我:**
那我去看一看吧，哈哈<br>
**我:**
进RouterContext.js这个文件了<br>
**我:**
我次奥了

>:expressionless::
>狗

**我:**
十行不到，我把他搞定，我就可以专注Router.js那个文件了。那个文件里面，就是全部Router的核心了<br>
**我:**
标准context用法，店长推荐的<br>
**我:**
返回Router.js了哈

>:expressionless::
>然后呢<br>
>:expressionless::
>看createContext么

**我:**
createContex就是最新的context用法，参加[这个](https://github.com/phgap/blogs/blob/master/react-router/00_20181102_context.md#api)<br>
**我:**
所以，需要有准备知识，哈哈<br>
**我:**
简单点说，就是一个提供者(Provider)，一个是消费者(Consumer)<br>
**我:**
我这次看的是react-router<br>
**我:**
别跑偏了<br>
**我:**
回到router.js去了<br>
**我:**
这个时候，可以稍微进入细节一些了<br>
**我:**
从第一个函数定义开始<br>
**我:**
从名字看，是获取context的，返回一个对象，多余的不知道，先放着，往后看

>:expressionless::
>嗯

**我:**
我先大概扫一眼组件都有哪些方法，除了组件，还有其他哪些代码<br>
**我:**
除了组件内容，组件下面有一个判断，看起来应该是处理老版本react的兼容问题的。那我就先不看了<br>
**我:**
所以，重点就是在这个组件里面了。组件里面就是一些声明周期函数<br>
**我:**
constructor、didMount<br>
**我:**
这俩，是初始化的地方

>:expressionless::
>嗯嗯

**我:**
一个一个看<br>
**我:**
重点是那个判断<br>
**我:**
if (!props.staticContext) {}的作用，是保证Router里面再嵌套Router时，使用的是相同的history<br>
**我:**
里面是一个监听，监听history中的location的改变，也就是说，当路径改变时，这个history是统一监听的

>:expressionless::
>嗯嗯

**我:**
那里面就调用了setState了呗，所以render就执行了

>:expressionless::
>嗯

**我:**
render非常简单，就是把context的value值，修改了一下

>:expressionless::
>嗯啊

**我:**
我们知道，只要context的value一变化，对应的consumer的函数，就会被调用，是吧

>:expressionless::
>嗯嗯

**我:**
那现在Router就结束了<br>
**我:**
接下来，我们好奇的是，哪些组件使用了Consumer

>:expressionless::
>找route

**我:**
对。根据React-router的使用，估计就是每个<Route>，都会监听这个context，然后进行路径匹配，决定是否要渲染自己的component指定的内容<br>
**我:**
接下来，我们就可以继续看<Route>这个组件了。先吃饭去吧，且听下回分解。


>:expressionless::
>嗯，好的。拜拜。