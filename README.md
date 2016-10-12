
> 本文从属于笔者的[](),项目的Github地址为[]().

> Warning!笔者自己构建的基于Webpack+React+Redux的脚手架已经经历了三个版本,分别参考[]()以及[]()。在本文之处,我必须严肃吐槽下,我深刻感觉到Boilerplate就像当年的Rails,方便入门的同时会给你无尽的束缚,因此笔者不建议任何人在大型项目中直接使用任何自己不能完全掌控的脚手架。我觉得我是无法忘记当初被react-redux-universal-hot-example支配的恐惧。

# Webpack2 React Redux Boilerplate

> 核心组件代码与脚手架之间务必存在有机分割，整个程序架构清晰易懂。

![](https://coding.net/u/hoteam/p/Cache/git/raw/master/2016/10/2/1-7M1eE4GO6_Lh2c-pmjGkcA.jpeg)

如果你是完全的React初学者,那么建议首先了解下[使用Facebook的create-react-app快速构建React开发环境](https://segmentfault.com/a/1190000006055973)

## Reference

参考的其他模板项目:
- [react-transform-boilerplate](https://github.com/gaearon/react-transform-boilerplate)
- [react-boilerplate](https://github.com/mxstbr/react-boilerplate)
- [react-redux-saga-boilerplate](https://github.com/gilbarbara/react-redux-saga-boilerplate)
- [react-redux-universal-hot-example](https://github.com/erikras/react-redux-universal-hot-example)

推荐阅读文章:
- [webpack-for-the-fast-and-the-furious](https://medium.freecodecamp.com/webpack-for-the-fast-and-the-furious-bf8d3746adbd#.poot9r5ee)
- [react-project](https://github.com/ryanflorence/react-project#lazy)




## Features

本部分假设你已经对Webpack有了大概的了解，这里我们会针对笔者自己在生产环境下使用的Webpack编译脚本进行的一个总结，在介绍具体的配置方案之前笔者想先概述下该配置文件的设计的目标，或者说是笔者认为一个前端编译环境应该达成的特性，这样以后即使Webpack被淘汰了也可以利用其他的譬如JSPM之类的来完成类似的工作。

- 考虑到同一项目对多编译目标的支持，包括开发需求、纯前端运行需求（包括Cordova、APICloud、Weapp这种面向移动端的方案）、同构直出需求，并且保证项目可以在这三个环境之间平滑切换，合理分割脚手架工具与核心应用代码。

- 单一的配置文件：很多项目里面是把开发环境与生产环境写了两个配置文件，可能笔者比较懒吧，不喜欢这么做，因此笔者的第一个特性就是单一的配置文件，然后通过npm封装不同的编译命令传入环境变量，然后在配置文件中根据不同的环境变量进行动态响应。另外，要保证一个Boilerplate能够在最小修改的情况下应用到其他项目。

- 多应用入口支持：无论是单页应用还是多页应用，在Webpack中往往会把一个html文件作为一个入口。笔者在进行项目开发时，往往会需要面对多个入口，即多个HTML文件，然后这个HTML文件加载不同的JS或者CSS文件。譬如登录页面与主界面，往往可以视作两个不同的入口。Webpack原生提倡的配置方案是面向过程的，而笔者在这里是面向应用方式的封装配置。

- 调试时热加载：这个特性毋庸多言，不过热加载因为走得是中间服务器，同时只能支持监听一个项目，因此需要在多应用配置的情况下加上一个参数，即指定当前调试的应用。

- 自动化的Polyfill：这个是Webpack自带的一个特性吧，不过笔者就加以整合，主要是实现了对于ES6、React、CSS(Flexbox)等等的自动Polyfill。

- 资源文件的自动管理：这部分主要指从模板自动生成目标HTML文件、自动处理图片/字体等资源文件以及自动提取出CSS文件等。

- 文件分割与异步加载：可以将多个应用中的公共文件，譬如都引用了React类库的话，可以将这部分文件提取出来，这样前端可以减少一定的数据传输。另外的话还需要支持组件的异步加载，譬如用了React Router，那需要支持组件在需要时再加载。

## 真的需要Redux吗?

对于这个问题笔者没有明确的答案,但是在这两年的自己对于Redux的实战中,也一直在摇把。我坚定的认为Redux指明了解决某类问题的正确方向,但是它真的适合于所有的项目吗?
笔者在[我的前端之路]()一文中提及,

## Personal Best Practice

- 使用Promise进行异步操作，建议使用await/async作为Promise语法糖构建异步函数。

- 使用fetch作为统一的数据获取函数，在本项目中使用了笔者的[fluent-fetcher]()作为fetch的上层封装使用。

- 尽可能少的使用行内样式,将每个组件的样式文件与组件声明文件同地存放。
譬如Material-UI这个著名的React样式组件库与[react-redux-universal-hot-example](https://github.com/erikras/react-redux-universal-hot-example)
之前的版本都是用的CSS-IN-JavaScript,全部内联样式。笔者感觉还是需要将CSS与JS剥离开来,一方面是处于职责分割的考虑,另一方面也是为了样式的可变性。通过样式类的方式来定义方式很方便地可以通过CSS来修正样式,而不需要每次都要找半天内联样式在哪里,然后去重新编译整个项目。

# Quick Start

use `npm install` / `npm link` to set up environment

use `npm start` to start Develop Server:localhost:3000

use `npm run storybook` to start StoryBook UI

use `npm run build` to build the release version

use `npm run build:style-check` to check code style and build the release version

use `npm run deploy` to build and set up a simple http server for the dist directory


# Develop Environment:开发环境机制详解

## Webpack2

> - [What's new in webpack 2](https://gist.github.com/sokra/27b24881210b56bbaff7)

## WebpackDevServer & Hot Loader

## API Proxy

# React Router & Server Side Rendering

## Pure Frontend 

这里有个关于React Router的点我想说明下,

## Server Side Rendering

（1）Server端只负责首页的渲染，其他页面仍然

（2）以Redux为代表的状态管理工具中的Store只是在第一次渲染时将数据传递给客户端，在后续的页面切换/认证等操作中的所有代码皆在客户端运行。


```
<div data-reactroot="" data-reactid="1" data-react-checksum="663537196">
    <section class="login__container" data-reactid="2"><!-- react-text: 3 -->登陆界面<!-- /react-text -->
        <div data-reactid="4utton data-reactid=" 5
        ">点击登陆</button>
        <button data-reactid="6">点击登出</button>
</div></section></div>
```


```
/**
 * Created by apple on 16/9/13.
 */
export class Login extends Component {

  /**
   * @function 默认渲染函数
   * @return {XML}
   */
  render() {
    return <section className="login__container">
      登陆界面

      <div>
        <button onClick={()=> {
          //将登陆信息写入cookies与localStorage
          login().then(()=> {
            //登陆成功跳转到详情页
            this.props.router.push('/detail');
          });
        }}>
          点击登陆
        </button>

        <button onClick={()=> {
          //将登陆信息写入cookies与localStorage
          logout();
          //登陆成功跳转到详情页
          this.props.router.push('/');
        }}>
          点击登出
        </button>
      </div>

    </section>
  }
}
```


与纯粹的前端项目相比,如果需要引入Server Side Rendering的支持,需要将本文的react_app.js这个入口文件替换为react_client.js,其核心代码变为了:

```
/**
 * Created by apple on 16/9/13.
 */
import React from 'react';
import { match, Router } from 'react-router';
import { render } from 'react-dom';
import { createHistory } from 'history';
import getRoutes from '../../react/routes';

const {pathname, search, hash} = window.location;
const location = `${pathname}${search}${hash}`;

// calling `match` is simply for side effects of
// loading route/component code for the initial location
match({routes: getRoutes(localStorage), location}, () => {
  render(
    <Router routes={getRoutes(localStorage)} history={createHistory()}/>,
    document.getElementById('app')
  )
});
```

而相对应的也需要添加react_server.js，其负责将

## Authentication

# Isomorphic Redux

对于[Redux Dev Tools](https://github.com/gaearon/redux-devtools#browser-extension),请自行使用[Browser Extension]()。

笔者目前在自己主导的几个前端项目中渐渐的转向MobX与Redux并行.本项目中对于Redux的文件布局采取的是[Ducks](https://github.com/erikras/ducks-modular-redux)这种方式,参考了[my-journey-toward-a-maintainable-project-structure-for-react-redux](https://hackernoon.com/my-journey-toward-a-maintainable-project-structure-for-react-redux-b05dfd999b5#.yot2mml9t)一文。即按照特性来将Reducers、ActionCreators、Actions、Selectors集中到单个文件中:
```
// src/ducks/auth.js
const AUTO_LOGIN = 'AUTH/AUTH_AUTO_LOGIN'
const SIGNUP_REQUEST = 'AUTH/SIGNUP_REQUEST'
const SIGNUP_SUCCESS = 'AUTH/SIGNUP_SUCCESS'
const SIGNUP_FAILURE = 'AUTH/SIGNUP_FAILURE'
const LOGIN_REQUEST = 'AUTH/LOGIN_REQUEST'
const LOGIN_SUCCESS = 'AUTH/LOGIN_SUCCESS'
const LOGIN_FAILURE = 'AUTH/LOGIN_FAILURE'
const LOGOUT = 'AUTH/LOGOUT'

const initialState = {
  user: null,
  isLoading: false,
  error: null
}

export default (state = initialState, action) => {
  switch (action.type) {
    case SIGNUP_REQUEST:
    case LOGIN_REQUEST:
      return { ...state, isLoading: true, error: null }

    case SIGNUP_SUCCESS:
    case LOGIN_SUCCESS:
      return { ...state, isLoading: false, user: action.user }

    case SIGNUP_FAILURE:
    case LOGIN_FAILURE:
      return { ...state, isLoading: false, error: action.error }

    case LOGOUT:
      return { ...state, user: null }

    default:
      return state
  }
}

export const signup = (email, password) => ({ type: SIGNUP_REQUEST, email, password })
export const login = (email, password) => ({ type: LOGIN_REQUEST, email, password })
export const logout = () => ({ type: LOGOUT })
```

## Pure Frontend 

引入Redux最大的区别在于,笔者所做的改造点为

### [React Router Redux](https://github.com/reactjs/react-router-redux)
React Router Redux的代码还是简单易懂的,其只是在用户点击/跳转与React Router自身的History之间加上了一层封装

> [history](https://github.com/reactjs/history) + `store` ([redux](https://github.com/reactjs/redux)) → [**react-router-redux**](https://github.com/reactjs/react-router-redux) → enhanced [history](https://github.com/reactjs/history) → [react-router](https://github.com/reactjs/react-router)

如果你需要自定义其他的Location,譬如如果你需要引入ImmutableJS作为Store:
```
import Immutable from 'immutable';
import {
    LOCATION_CHANGE
} from 'react-router-redux';

let initialState;

initialState = Immutable.fromJS({
    locationBeforeTransitions: undefined
});

export default (state = initialState, action) => {
    if (action.type === LOCATION_CHANGE) {
        return state.merge({
            locationBeforeTransitions: action.payload
        });
    }

    return state;
};
```
## SSR


> 欢迎大家指导与讨论,同时再次建议,在不能掌握本项目的情况慎重直接用于大型项目中,对自己负责。