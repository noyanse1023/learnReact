# React 路由
## BrowserRouter 在app.js中包裹着其他的组件
```
render () {
  return (
    <BrowserRouter>
      <div className="App">
        <Blog />
      </div>
    </BrowserRouter>
    )
}
```
## Swicth 只加载一个路由 按先后顺序
## NavLink 点击跳转的按钮
## exact
```
<NavLink
  to="/"
  exact // 在root url，都会影响到, 用子路由就没事
  activeClassName="my-active" // 添加自定义高亮 class
  activeStyle={{ // 添加自定义style
    color: '#58a'
    }}
>
</NavLink>
```
Blog.js
```
import { Route, NavLink, Swicth } from 'react-router-dom'
<Route path="/" exact component={Posts} /> // 每次都会加载
<Switch>
// 这两个只加载一个
  <Route path="/new-post" component={newPost} />
  <Route path="/:id" exact component={FullPost} />
</Switch>
```
Posts组件：
```
import { Link } from 'react-router-dom'
<Link to={'/' + post.id} key={post.id}> //跳转到详情页
  <Post
    title={post.title}
    author={post.author}
    clicked={() => this.postSelectedHandler(post.id)}
  </Post>
</Link>
```
# 取代link的一种方法, 大多数用这种
* 有时候需要在做一些事情都在导航过去，比如 HTTP request 发送后
  有一个push方法，让你push一个新的页面在页面的堆栈中，因为导航就是基于一堆页面的。这也是浏览器为什么有后退前进按钮，因为你可以回到上一个页面或者重新进来页面。可以用`this.props.history.push()` 参数可以是string或者object
Posts组件：
```
postSelectedHandler = (id) => {
  this.props.history.push(pathname: '/' + id) // 字符串
  // this.props.history.push({pathname: '/' + id}) // 对象

}
render () {
  <Post
    title={post.title}
    author={post.author}
    clicked={() => this.postSelectedHandler(post.id)}
  </Post>
}

```

# 嵌套路由 nested route
Blog.js
```
import { Route, NavLink, Swicth } from 'react-router-dom'
// <Route path="/" exact component={Posts} /> // 每次都会加载
<Switch>
// 这两个只加载一个
  <Route path="/new-post" component={newPost} /> // 先加载这个
  // 把FullPost路由放到里面
  <Route path="/" component={Posts} /> // 去掉exact

</Switch>
```
posts.js
```
import { Route } from 'react-router-dom'
import FullPost from './FullPost'
render (
  <div>
    <section>
      {posts}
    </section>
    // 动态匹配详情页地址前缀
    <Route path={this.props.match.url + '/:id'} exact component={FullPost}
  </div>
  )
```

# 创建动态嵌套路由
- 组件自身不会改变，非常的低效 在 unmount 和 mount的时候
因为componentDidMount不会再次触发，所以不会再次请求数据
- 修复这个，我们应该执行 componentDidUpdate,会被再次执行
- Number !== +字符串
```
componentDidMount () {
  console.log(this.props)
  this.loadData()
}
```
componentDidUpdate () {
  this.loadData
}
loadData () {
  // 请求数据
}

# 重定向 Redirect
```
import { Route, NavLink, Switch, Redirect } from 'react-router-dom'
<Redirect from="/" to="/posts" /> // 改变url
```

# 有条件的重定向
- 当我们点击submit按钮或者我们请求数据，我们想改变改变路由，不是在当前页面
```
import { Redirect } from 'react-router-dom'

render () {
  let redirect = null
  // submitted 在按下提交后设置为true
  if(this.state.submitted) {
    redirect = <Redirect to="/posts" />  
  }
  return (
    {redirect}
    )
}
```

# 使用历史Prop 重定向(replace) 某个方法后，改变路由, 替换上面的办法
- this.props.history.push('/posts')
newPost.js
```
  this.props.history.push('/posts') 或者
  this.props.history.replace('/posts')

  // 直接在按下提交按钮后， 重定向
```

# 导航守卫
- 你不知道用户有没有权限
如果路由没render,你can't reach that route

```
state = {
  auth: false
}
this.state.auth ? <Route path="/new-post" component={NewPost} /> : null
```

# 处理404

```
<Route render={() => <h1>Not found</h1>}

```
