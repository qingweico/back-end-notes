[TOC]

## Redux Toolkit(RTK)

安装

npm run @reduxjs/toolkit

### RTK API

- configureStore()  包装了 createStore, 提供简化的配置选项以及默认值, 其默认包含了redux-thunk
- createReducer() 用于创建Redux中的 reducer 函数, 可以帮助简化和组织 reducer 的编写
- createAction()  定义一个标准的 action creator 函数, 可以生成符合 Flux 标准的 action 对象
- createSlice() 接受一个或者多个 Reducer 函数对象, 一个切片名称和一个初始状态值, 并自动生成一个具有相应action creator和动作类型的切片 Reducer, 其中使用了 Immer 库来简化 Redux 中的状态更新操作
- combineSlices 用于简化和增强 `combineReducers` 的功能 (不是 Redux 核心库的一部分)
- createAsyncThunk 用于创建异步的 thunk actions, 简化了异步逻辑的创建和管理
- createEntityAdapter() 是 Redux Toolkit 提供的一个工具, 预构建的 Reducer 和 Selector, 用于在规范化状态下执行 CRUD 操作, 用于简化和标准化处理规范化数据的流程
- createSelector() 用于创建可记忆化(memoized)的选择器函数, 其用于从 Redux store 中获取和计算派生状态, 以供 React 组件使用

### Redux的核心API

- createStore 创建一个 Redux 存储
- combineReducers 用于将多个 reducer 函数组合成一个单一的 reducer 函数, 这个单一的 reducer 函数可以传递给 Redux store
- applyMiddleware 用于在创建 Redux store 时添加中间件(中间件提供了一种扩展 Redux 功能的方法,例如处理异步操作、记录日志)
- compose 是 Redux 的一个辅助函数,用于组合多个函数以形成一个新的函数,从右到左的顺序依次调用每个传入的函数,其在redux 中的使用场景为通常和`applyMiddleware` 函数以及 `Redux DevTools Extension`结合使用, 以创建一个 Enhancer,用于配置 Redux store

### redux-thunk

一个用于Redux的中间件, 用于处理异步操作, 使得Redux的action creators能够返回一个函数(thunk)而不是一个普通的action对象

核心点: 使用切片对象生成actions以及reducer

使用 api `createSlice`(自动生成action关并联对应reducer)

## RTK Query

RTK Query 是一款高级数据获取和缓存工具,是@reduxjs/toolkit中的可选插件, 即 RTK Query 包含在 RTK 中, 其实现建立在 Redux Toolkit 核心之上, 并在其架构内部使用Redux, 由TypeScript编写

### API

- `createApi` RTK Query 功能的核心

- fetchBaseQuery() 一个小型包装器[`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

- ApiProvider 通常作为一个顶级组件或者上下文提供者, 用于管理和提供应用程序所需的 API 服务或者数据服务

- setupListeners 用于启用`refetchOnMount`和`refetchOnReconnect`行为

refetchOnMount 是一个 React Query 库中的选项, 用于指定在组件挂载(mount)时是否重新获取数据

refetchOnReconnect 是 React Query 中的另一个选项, 用于指定在重新连接到网络后是否重新获取数据

## 问题

### React-Redux 与 Redux 库的区别

首先这是两个相关但不同的库

- Redux 是一个用于JavaScript应用程序的状态管理库,并不特定于React, 可以与任何JavaScript框架或库一起使用
- React-Redux 是一个官方的Redux绑定库, 其提供了一些工具, 使得在React组件中访问和操作Redux状态更加简单和直观

### redux-logger

一个用于Redux的中间件,用于日志记录Redux状态变化和action

### extraReducers

用于处理 slice 之外的 action 的工具, 使得能够在一个 slice 中响应其他 slice 的 action 或异步 action

使用场景

- 处理其他 slice 的 action
- 处理异步 action, 通常结合 `createAsyncThunk` 使用

### Immer 库

作用: Immer使得对不可变(immutable)数据结构的操作看起来像是直接修改数据一样, 通过使用基于结构共享的持久性数据结构来实现这一点, 以提高性能

优点

- 简化了 reducer 的编写
- 避免了手动进行深拷贝和展开