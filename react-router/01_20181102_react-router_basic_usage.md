# 2. React-Router的基本使用

## ~~Basic Components~~
## 基本组件

~~There are three types of components in React Router:~~
* ~~router components~~ 
* ~~route matching components~~
* ~~navigation components~~

React Router中有三类组件:
* **router**组件
* **route matching**组件
* **navigation**组件

~~All of the components that you use in a web application should be imported from **react-router-dom**.~~
我们在web应用中使用的这些组件，都可以从**react-router-dom**中导入得到。
```jsx
import { BrowserRouter, Route, Link } from "react-router-dom";
```

### Routers
~~At the core of every React Router application should be a router component. For web projects, react-router-dom provides <BrowserRouter> and <HashRouter> routers. Both of these will create a specialized history object for you. Generally speaking, you should use a <BrowserRouter> if you have a server that responds to requests and a <HashRouter> if you are using a static file server.~~
基于React Router的web应用，是一个router组件。 web项目中，**react-router-dom**提供了<BrowserRouter>和<HashRouter>两种路由。两种路由都会创建一个history对象。通常情况下，如果我们有响应请求的服务器，我们应该使用<BrowserRouter>; 如果使用静态文件服务器，我们使用<HashRouter>

```jsx
import { BrowserRouter } from "react-router-dom";
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  holder
);
```

### Route Matching
There are two route matching components: <Route> and <Switch>.import { Route, Switch } from "react-router-dom";
```jsx
import { Route, Switch } from "react-router-dom";
```

~~Route matching is done by comparing a <Route>'s path prop to the current location’s pathname. When a <Route> matches it will render its content and when it does not match, it will render null. A <Route> with no path will always match.~~

路由匹配是通过将<Route>组件的path属性与当前的location的pathname进行比对完成的。当一个<Route>匹配了，它的内容将被渲染出来。
否则，会渲染null。如果一个<Route>没有path属性，他的内容将默认一直被渲染出来。

```jsx
<Route path='/about' component={About}/> // renders <About/>
<Route path='/contact' component={Contact}/> // renders null
<Route component={Always}/> // renders <Always/>
```
~~You can include a <Route> anywhere that you want to render content based on the location. It will often make sense to list a number of possible <Route>s next to each other. The <Switch> component is used to group <Route>s together.~~

我们可以在组件树的任何位置放置<Route>组件。但是更常见的情况是将几个<Route>写在一起。<Switch>组件可以用来将多个<Route>组织在一起。

~~The <Switch> is not required for grouping <Route>s, but it can be quite useful. A <Switch> will iterate over all of its children <Route> elements and only render the first one that matches the current location. This helps when multiple route’s paths match the same pathname, when animating transitions between routes, and in identifying when no routes match the current location (so that you can render a “404” component).~~

多个组件在一起时，并不强制要求使用<Switch>，但是使用<Switch>可能是非常方便的。<Switch>会迭代所有的<Route>子组件，并只渲染第一个path属性与当前location匹配的<Route>。
```jsx
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
</Switch>

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
  {/* when none of the above match, <NoMatch> will be rendered */}
  <Route component={NoMatch} />
</Switch>
```

### Route Rendering Props
~~You have three prop choices for how you render a component for a given <Route>: component, render, and children. You can check out the <Route> documentation for more information on each one, but here we’ll focus on component and render because those are the two you will almost always use.~~
<Route>的componet属性，有三种写法：
* [component](https://reacttraining.com/react-router/web/api/Route/component)
* [render](https://reacttraining.com/react-router/web/api/Route/render-func)
* [children](https://reacttraining.com/react-router/web/api/Route/children-func)

具体用法可以参照<Route>文档，这里我们先简单介绍一下component和render。

~~component should be used when you have an existing component (either a React.Component or a stateless functional component) that you want to render. render, which takes an inline function, should only be used when you have to pass in-scope variables to the component you want to render. You should not use the component prop with an inline function to pass in-scope variables because you will get undesired component unmounts/remounts.~~

在使用<Route>时，如果我们想渲染的内容已经有对应的组件，则应该使用[component](#https://reacttraining.com/react-router/web/api/Route/component)的方法；[render](#https://reacttraining.com/react-router/web/api/Route/render-func)方法使用一个内联函数。不要将component属性设置为一个函数，然后在其内部渲染组件。这样可能会的引起组件意外的挂载/卸载动作。

```jsx
const Home = () => <div>Home</div>;

const App = () => {
  const someVariable = true;

  return (
    <Switch>
      {/* these are good */}
      <Route exact path="/" component={Home} />
      <Route
        path="/about"
        render={props => <About {...props} extra={someVariable} />}
      />
      {/* do not do this */}
      <Route
        path="/contact"
        component={props => <Contact {...props} extra={someVariable} />}
      />
    </Switch>
  );
};
```

### Navigation
~~React Router provides a <Link> component to create links in your application. Wherever you render a <Link>, an anchor (<a>) will be rendered in your application’s HTML.~~
React Router提供了一个<Link>组件用来在应用中添加link。当<Link>渲染时，一个<a>标签在html页面中被创建出来。
```jsx
<Link to="/">Home</Link>
// <a href='/'>Home</a>
```

~~The <NavLink> is a special type of <Link> that can style itself as “active” when its to prop matches the current location.~~
<NavLink>组件是一个特殊的<Link>组件。当它的path与当前location匹配时，可以自定义其样式。
```jsx
// location = { pathname: '/react' }
<NavLink to="/react" activeClassName="hurray">
  React
</NavLink>
// <a href='/react' className='hurray'>React</a>
```

~~Any time that you want to force navigation, you can render a <Redirect>. When a <Redirect> renders, it will navigate using its to prop.~~

当需要页面重定向时，我们可以使用<Redirect>组件。当一个<Redirect>组件被渲染时，页面将被渲染到<Redirect>组件的to属性指向的位置上。
```jsx
<Route exact path="/" render={() => (
  loggedIn ? (
    <Redirect to="/dashboard"/>
  ) : (
    <PublicHomePage/>
  )
)}/>
```