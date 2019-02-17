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
# Link
页面发送一个请求，获得一个新的页面
- 当点击link的时候，不要reload页面,不会丢失state
- 不发请求，用js reload
```
<Link to="/">Home</Link>
<Link to="{{
  pathname: '/new',
  hash: '#submit',
  search: '?quick-submit=true'
}}">Home</Link>
<Link to={props.match.url + '/new'}></Link> 相对路径

```
# 添加高亮
```
<NavLink to="/" exact>home</NavLink>
```
- 用自定义高亮
```
<NavLink 
  to="/" 
  exact 
  activeClassName="my-active" // 自定义class
  activeStyle={{ // 行内样式
    color: '#58a'
  }}
  >home</NavLink>

```
# 传递路由参数
```
<Route path='/new-post' component={NewPost} />
<Route path='/:id' exact component={Posts} />
// Link
render () {
  let posts = <p>出错</p>
  if(!this.state.error) {
    posts = this.state.posts.map(post => {
      return (
          <Link to={'/' + post.id}>
          
          </Link>
      )
    })
  }
}
<Link to={'/} + post.id> </Link>
```
# 拿到路由参数
```
componentDidMount() {
  console.log(this.props)
  if(this.props.match.params.id) {
    // 请求数据
  }
}
```

# 拿到query参数
```
<Link to="/my-path?start=5">Go to Start</Link> or
<Link 
    to={‌{
        pathname: '/my-path',
        search: '?start=5'
    }}
    >Go to Start</Link>

props.location.search 可以拿到 But that will only give you something like ?start=5 

拿到 key-value
componentDidMount() {
    const query = new URLSearchParams(this.props.location.search);
    for (let param of query.entries()) {
        console.log(param); // yields ['start', '5']
    }
}
```
Vanilla JS is a joke，就是指原生JS
URLSearchParams  is a built-in object, shipping with vanilla JavaScript. It returns an object, which exposes the entries()  method. entries()  returns an Iterator - basically a construct which can be used in a for...of...  loop (as shown above).

```
<Link to="/my-path#start-position">Go to Start</Link> 

or

<Link 
    to={‌{
        pathname: '/my-path',
        hash: 'start-position'
    }}
    >Go to Start</Link>
React router makes it easy to extract the fragment. You can simply access props.location.hash
```


## Swicth 只加载一个路由 按先后顺序
## NavLink 点击跳转的按钮, 可以给按钮添加样式 相当于 a 链接
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
exact 也可通过父组件传过来
```
<NavigationItem link="/" exact>aaa</NavigationItem>
<NavigationItem link="/orders" exact>bbb</NavigationItem>
NavigationItem.js:
const navigationItem = (props) => {
    <li className={classes.NavigationItem}>
        <NavLink
            to={props.link}
            exact={props.exact}
            activeClassName={classes.active}>
            {props.children}
        </NavLink>
    </li>
}
```

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

# withRouter
本质上是个高阶组件， 为了获取location这些参数而已
不是所有需要路由参数的组件都直接与路由相连，withRouter就是解决这个问题的
比如：
```
<Route exact path="/Home" component={Home}/>
```
1.只有包裹在Route组件里的才能使用`this.props.location`，
2.假如有个需求，是面包屑或者导航组件里需要拿到`this.props.location`（导航组件或者面包屑一般不会包裹在`Route`里吧），那么直接这么写显然就不行了。
这个时候`withRouter`修饰一下，就可以这么写了。
```
import { withRouter } from 'react-router-dom'
// ...

export default widthRouter(组件名)
```

# encodeURLComponent()
js 全局对象
可以把字符串作为 url 组件进行编码
- 语法
```
encodeURLComponent(URLstring)
```
URLstring 必须，一个字符串，含有 URI 组件或其他要编码的文本

# Aux文件
```
const aux = (props) => props.children

export default aux
```

# Redux 独立于react
## understanding state
it determines what we need to render to screen
- Ingredients added to Bugger?
- Is User Authenticated?
- Is a Modal open? Pure UI only state

## State Can Be Complex
- 组件A传给组件B 要用 路由参数---不够优雅
为什么不用全局对象呢？
因为 react 的 reactivity system doesn't react to changes in some global variable you defined

## understanding the redux flow
- `Central Store` 储存应用的state, 可以想象成一个巨大的js对象
- 组件`Component`获取或者操作当前应用的state, 不能直接操作store
- `Action`他们在组件中dispatcn,action知识一些信息的，比如，type:add/remove等。Pre-defined,information package(possibly with payload)
比如：the action is addIngredient,要传一些参数,也包含在action
action 没有逻辑，不知道如何更新store，just a messager
- `Reducer`改变store的是reducer，包哈type，reducer可以check the action
比如，我们定义一个action的type,reducer是一个纯函数，接收action和old state作为参数,可改变state的
reducer必须执行同步代码，没有异步， no asynchronous, no side effects, no HTTP request, nothing of that
之后会学到怎么执行异步代码,但是reducers, just input in, output out, nothing in between, no delay
reduer spits up the updated state which then is stored in the store again and replaces the old state and that has to be done in an ** immutable way**, so we always return a new state which can be based on the old one but which is technically a new js object, because objects are reference types in js and we want to make sure that we don't accidentally change the old one
reducer 返回一个新的对象，因为js对象是引用类型，要保证不会意外改变旧状态
that's how reduer handle the action
now the store is up to date
How do we get the updated state back into our component then?
仓库的状态改变了，我们的组件怎么获取改变的值呢
For that, we use a subscription model
用发布订阅的模式
triggers 触发
The store triggers all subscriptions whenever the state changes, whenever the state is updated in the store. And of course our compoment can subscribe to store updates and it then recreives that update automatically
It works through a subscription model and we simply say 'hey, I want to get notified whenever the state changes' just as we say 'Hey I want to change the state here is an action describing my plans'
当状态改变的时候,仓库触发所有的订阅,组件可以订阅仓库的改变，这样就可以自动接收啦
This is redux flow, this is how redux works

## 在Node中使用redux
1. `npm install --save redux`
2. `touch redux-basics.js`
```
const redux = require('redux')
const createStore = redux.createStore

// State
const initialState = {
    count: 0
}

// Reducer 是唯一更改state的途径,是离store最近的
const rootReducer = (state = initialState, action) => {// state默认参数
    if (action.type === 'INC_COUNTER') {
        // state.count++ // 这个 not immutable, shoudn't mutating the original state here, so what you instead do is you return a new js obj you may first copy the old state with the spread operator, then overwrite the one property you want to adjust

        return {
            ...state,
            count: state.count + 1
        }

    }
    if (action.type === 'ADD_COUNTER') {
        return {
            ...state,
            count: state.count + action.value
        }
    }
    return state
}

//Store
const store = createStore(rootReducer) // store要用reducer初始化，我们只有一个reducer
console.log(store.getState()) // 获取state

// Dispatching Action
store.dispatch({type: 'INC_COUNTER'})
store.dispatch({type: 'ADD_COUNTER', payload: {}, value: 10})
console.log(store.getState())

// Subscription
  // make sure that I don't have to manually call getState in my
  code if i want to get the current state snaoshot but to inform
  me whenever i need to get a new state because sth changed, because
  if i manually(手动地) do it like (store.getState()) in a console log
  I awalys have to guess if someting changed

  // suscribe 参数是回调函数，当状态更新后调用
  subscribe takes an argument, a function which will be executed when whenever the state is updated, so whenever an action reached the reducer. The function we passed to subscribe doesn't get any arguments
  // and then in the function body, we can execute any code we want on state updates
  // store.getState(), the difference is that i now know that I should get the state here because i know here sth changed

  // Subscription actually, of course typically is set up right after the store was created so that we get informed about any future dispatches

  // so i notice that subscribe comes before dispatching the actions and this function in the subcribe method will be executed whenever action is dispatched and mutates the store

  // It's getting triggered whenever the state is updated

  store.subcribe(() => { // 回调无参数
      // 这里可以肯定state改变了
      console.log('[Subscription]', store.getState())
  })

```

# Connecting React to Redux
index.js:
```
import { createStore } from redux
import reduer from './store/reduer.js'

const store = createStore(reduer)


```

store/reducer.js:
```
const initialState = {
    count: 0
}
const reducer = (state = initialState, action) => {
    if (action.type === 'INC_COUNTER') {
        return {
            ...state,
            count: state.count + 1
        }
    }
    return state
}

export default reducer
```

# Connecting the store to react
1. `npm install --save react-redux`
index.js:
```
import { createStore } from redux
import { Provider } from 'react-redux'

import reduer from './store/reduer.js'

const store = createStore(reduer)

ReactDOM.render(<Provider store={store}><App /></Provider>, ...)

```
2. 如何在组建中获取state
Counter.js
```
import { connect } from 'react -redux'
// connect is a function which returns a function which takes then a components as input
// connect is not really a higher order component, is a function which return s a higher order compoment

render() {
    return (
        <div>
            <CounterOutput value={this.props.ctr} />
        </div>
        )
}

// name is up to you
const mapStateToProps = state => {
    return {
        ctr: state.count // redux 中的 state
    }
}

export default connect(mapStateToProps)(Counter)

```

# Dispatching actions from within compoment
Counter.js:
```
render() {
    return (
        <div>
            <BtnControl clicked={this.props.onInCrementCounter} />
        </div>
        )
}

// name is up to you
const mapStateToProps = state => {
    return {
        ctr: state.count // redux 中的 state
    }
}
const mapDispatchToProps = dispatch => {
    return {
        onInCrementCounter: () => dispatch({ type: 'INC_COUNTER' })
    }
}

// 如果你只要传递action 第一个参数设置为 null

export default connect(mapStateToProps, mapDispatchToProps)(Counter)
```

# 传参
Counter.js:
```
const mapDispatchToProps = dispatch => {
    return {
        onInCrementCounter: () => dispatch({ type: 'INC_COUNTER', value: 10 }),
        onAddCounter: () => dispatch({ type: 'ADD_COUNTER', payload: {})
    }
}
```
reducer.js
```

const initialState = {
    count: 0
}
const reducer = (state = initialState, action) => {
    if (action.type === 'INC_COUNTER') {
        return { // 返回一个新对象
            count: state.count - action.value
        }
    }
    return state
}

export default reducer

```

# update state immutably
加一个 store Result 按钮
Counter.js:

```
<ul>
    {this.props.storedResults.map(strResult => (
        <li key={strResult.id} onClick={this.props.onStoreResult}>{strResult.val}</li>
        ))}

</ul>
const mapStateToProps = state => {
    return {
        storedResults: state.results
    }
}
const mapDispatchToProps = dispatch => {
    return {
        onStoreResult: () => dispatch({type: 'STORE_RESULT'})
    }
}
```
reducer.js
// concat 返回一个新数组, 不要用改变原数组的方法push,splice。。。
```

const initialState = {
    count: 0,
    results: []
}
const reducer = (state = initialState, action) => {
    switch(action.type) {
        case 'STORE_RESULT':
            return {
                ...state,
                results: state.results.concat({id:new Date(), val: state.counter})
            }
    }
    return state
}

export default reducer

```

# outsourcing action types
actions.js
```
export const INCOREMENT = 'INCOREMENT'
export const DECOREMENT = 'DECOREMENT'
export const ADDOREMENT = 'ADDOREMENT'
```

reducer.js
```
import * as actionTypes from './actions'

switch(action.type) {
    case actionTypes.INCOREMENT
}
```
Counter.js
```
import * as actionTypes from './actions'
const mapDispatchToProps = dispatch => {
    return {
        onStoreResult: () => dispatch({type: actionTypes.INCOREMENT})
    }
}
```

# Combining Multiple Reducers
store/reducers/counter.js
store/reducers/results.js

index.js
```
import { createStore, combineReducers } from 'redux'
import counterReducer from './store/reducers/counter.js'
import resultsReducer from './store/reducers/results.js'

const rootReducer = combineReducers({
    ctr: counterReducer,
    res: resultsReducer
    })
const store = createStore(rootReducer)
```

Counter.js
```
const mapStateToProps = state => {
    return {
        ctr: state.ctr.count,
        storedResults: state.res.results
    }
}
const mapDispatchToProps = dispatch => {
    return {
        onStoreResult: (result) => dispatch({type:actionTypes.RESULTS, result:result})
    }
}
```
result.js
```
value: action.result
```
<Button onClick={() => this.props.onStoreResult(this.props.ctr)} >


# understanding state types
当用户刷新页面的时候，数据消失
shoud every state can be handled through redux ?
Well the question whether you use redux or not depends on the size of your application and the complexity of your state
1. Local UI State  ---------- Mostly handled withiin components 不用
 - show/hide backdrop

2. Persistent State
 - All Users/all Posts,... ---Stored on Server, relevant slice 不用 managed by Redux

3. Client State
 - Is Authenticated? Filters set by User... ------Managed via Redux用
