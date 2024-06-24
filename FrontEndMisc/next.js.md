## Next.js

[TOC]

## 快速入门

使用 `nvm` 安装 `node`

```bash
# 列出所有远程服务器的版本
nvm list available
# 安装指定版本的 node
nvm install 20.8.0
# 使用指定版本的 node
nvm use 20.8.0
```

创建项目

```bash
npx create-next-app@latest ${项目名称}
```

进入新创建的项目目录

```bash
cd my-next-app
```

启动开发服务器

```bash
npm run dev
```

项目结构

```txt
my-next-app/
├── node_modules/ 	       // 项目所有的依赖包
├── public/			       // 静态资源,如图片、favicon 等
├── src/				   // 源代码文件
├── app/
│   ├── favicon.ico		   // 网站的图标文件
│   ├── globals.css		   // 全局样式文件
│   ├── layout.tsx		   // 页面的布局组件
│   ├── page.tsx		   // 主页
│   ├── .eslintrc.json	   // eslint 配置文件
│   ├── .gitignore		   // git忽略文件
│   ├── next.config.mjs	   // next.js 配置文件
│   ├── next-env.d.ts	   // next.js TypeScript 声明文件
│   ├── package.json	   // 项目配置文件
│   ├── package-lock.json  // 依赖锁定文件
│   ├── postcss.config.mjs // postcss 配置文件
│   ├── README.md		   // readme 文件
│   ├── tailwind.config.ts // Tailwind CSS 配置文件
│   ├── tsconfig.json	   // TypeScript 配置文件
```

## 使用样式

### Tailwind CSS

安装 

```bash
npm install tailwindcss
```

配置

```js
// tailwind.config.js 文件
/** @type {import('tailwindcss').Config} */
export default {
    content: [
        "./src/**/*.{js,ts,jsx,tsx,mdx}"
    ],
    theme: {
        extend: {},
    },
    plugins: [],
    corePlugins: {
        preflight: false,
    },
}
```

创建一个 CSS 文件  命名为 global.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

```tsx
// 引入 global.css 文件
// import "${你的global.css文件所在路径}";
// page.tsx
export default function Page() {
    return <h1 className="text-red-500 text-3xl font-bold underline">Hello, Next.js!</h1>
}
```

### styled-component(CSS-in-JS 库)

`styled-component`是一个结合了 JavaScript 和 CSS、允许在JavaScript 文件中直接定义和管理样式的样式库, 实现组件级别的样式管理

安装

```bash
npm install styled-components
```

导入

```tsx
import styled from "styled-components"
export const AppFooterWrapper = styled.div`
  width: 100%;
  height: 100px;
  text-align: center;
  line-height: 100px;
  color: #f8f8f8;
  background-color: '#2c3e50';
  transition: background-color .2s;
  `
```

```tsx
<AppFooterWrapper/></AppFooterWrapper>
```

### 其他

使用`clsx`动态添加或移除 CSS 类名

`clsx` 是一个用于动态生成类名的 JavaScript 函数

下面是一个使用`clsx`的简单示例

```tsx
import clsx from 'clsx';
// 使用clsx库切换类名
export default function InvoiceStatus({status}: { status: string }) {
    return (
        <span
            className={clsx(
                'inline-flex items-center rounded-full px-2 py-1 text-sm',
                {
                    'bg-gray-100 text-gray-500': status === 'pending',
                    'bg-green-500 text-white': status === 'paid',
                },
            )}
        > {status === 'pending' ? '待支付' : '已支付'}
        </span>

    )
}
```

## 路由

Next js 使用 "文件系统路由"(基于文件和目录结构自动生成路由的机制, 不需要手动配置每个路由, 只需根据文件和文件夹的结构来组织页面文件)

- 应用路由
- 页面路由

Next.js 中有四种方式可以在路由之间导航

- 使用<Link>组件(内置组件)
- 使用useRouter钩子(客户端组件)
- 使用redirect函数(服务器组件)
- 使用原生History API

下面是使用<Link>组件的示例

注意: Next.js 中分为服务器组件和客户端组件, Next.js默认使用服务器组件

要使用客户端组件需要在组件顶部申明 'use client' 显示使用客户端组件

```tsx
'use client'
import Link from 'next/link';
const links = [
    {name: '菜单管理', href: "/system/menu"},
    {name: '角色管理', href: "/system/role"},
    {name: '权限管理', href: "/system/prem"}
]

export default function NavLinks() {
    return (
        <>
            {links.map((link) => {
                return (
                    <Link
                        key={link.name}
                        href={link.href}
                        className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
                    >
                        <p className="hidden md:block">{link.name}</p>
                    </Link>
                )
            })}
        </>
    );
}
```

下面是使用useRouter钩子的示例

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function JumpToDashboard() {
    const router = useRouter()

    return (
        <button type="button" onClick={() => router.push('/dashboard')}>
            跳转到 Dashboard
        </button>
    )
}
```

## 数据获取

### 使用`fetch`

`Next.js` 扩展了原生 `fetch` API, 提供了可配置选项(缓存和重新验证)

`AbortController`是一个用于控制 Fetch 请求的 API, 它可以实现取消请求的功能

```tsx
async function getData() {
    const { signal } = new AbortController()
    const res = await fetch('http://localhost:5000/user/get', {
        signal,
        // 基于时间的重新验证
        next: { revalidate: 3600 },
        headers: {
            'Content-Type': 'application/json'
        },
        // 强制使用缓存
        cache: 'force-cache'
        // 不使用缓存
        // { cache: 'no-store' }
    })
    console.log("getData")
    if (!res.ok) {
        throw new Error('Failed to fetch data')
    }
    return res.json()
}

export default async function FetchPage() {
    const data = await getData()
    return (<div>
        <p>{data.id}</p>
        <p>{data.username}</p>
        <p>{data.address}</p>
        <p>{data.mobile}</p>
    </div>)
}
```

### 使用`axios`

```tsx
import { AxiosResponse } from "axios"
// 自定义 Axios 响应体
export interface CustomAxiosResponse extends AxiosResponse {
  code?: number;
  msg?: string;
  total?: number;
}
```

```tsx
// 创建 Axios 响应
const service = axios.create({
  baseURL: 'http://localhost:5000',
  timeout: 5000,
  withCredentials: true
})
```

```tsx
// 配置 get 请求
export const get = (url: string, params: any = {}) => {
  return new Promise((resolve, reject) => {
    service.get(url, params).then((res: CustomAxiosResponse) => {
      resolve(res);
    }, err => {
      errorMessage("网络错误")
      reject(new Error(err))
    })
  })
}
```

## 优化

### 图像优化

使用内置的`<Image>`

```tsx
import Image from 'next/image';
   <Image
      alt='登录背景图片'
      width={400}
      height={300}
      className='container-top'
      src={require("@/assets/img/login.jpg")} />
```

## redux

`Redux` 是一个用于管理 JavaScript 应用状态的库

关键概念: `Store`、`Actions`、`Reducers`, `Dispatch`

类比 VUE 中的 `store`, `Mutations`, `Actions`

- Store是一个对象,用来保存应用中所有state信息的(整个应用只能有一个store) 通过`createStore(reducer)`创建
- Actions 也是一个对象, 包括 type 属性 和 data
- Reducers `Reducers` 是纯函数,可以理解为处理器, 接收当前的 state 和 action, 根据不同的action 决定如何更新 state
- Dispatch 是 store 的一个方法, 用于发送 action
- Subscription 订阅, 也是 store 的一个方法 监听 state 的变化, 调用订阅的回调函数
- Middleware 是在 action 被发起之后, 到达 reducer 之前的扩展

过程: 通过dispatch方法发送action到store, store 交给 reducer 处理, 更新 State, 通知所有订阅了 state 变化的组件

示例一: 计数器

```js
// actions.js
export const increment = () => ({
    type: 'INCREMENT',
});

export const decrement = () => ({
    type: 'DECREMENT',
});
```

```tsx
// store.js
import { createStore } from 'redux';

const initialState = {
    count: 0,
};
// 定义 reducer
const counterReducer  = (state = initialState, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return { ...state, count: state.count + 1 };
        case 'DECREMENT':
            return { ...state, count: state.count - 1 };
        default:
            return state;
    }
}
// 创建Redux store
const store = createStore(counterReducer);
export default store;
```

```js
// Counter.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './actions';

const Counter = () => {
    // 使用useSelector从store中获取状态
    const count = useSelector((state) => state.count);
    // 使用useDispatch获取dispatch函数
    const dispatch = useDispatch();

    return (
        <div>
            <h1>Count: {count}</h1>
            <button onClick={() => dispatch(increment())}>Increment</button>
            <button onClick={() => dispatch(decrement())}>Decrement</button>
        </div>
    );
};

export default Counter;
```

示例二: 用户登录后储存用户信息 登出后置空用户信息

```ts
export enum ActionTypes {
  USER_LOGIN = 'USER_LOGIN',
  EXIT = 'EXIT',
}
```

扩展 Redux 的 `Action` 接口是为了满足特定的需求 定义一个可以携带任意类型数据的 data

```tsx
// action.ts
import type { Action } from "redux"

export interface ActionExternal extends Action {
  data?: any
}
```

```tsx
// reducer.ts
const initialState = Map({
  userInfo: null
})

function reducer(state = initialState, action: ActionExternal) {
  switch (action.type) {
    case ActionTypes.USER_LOGIN:
      return state.set("userInfo", action.data);
    case ActionTypes.EXIT:
      return state.set("userInfo", null)
    default:
      return state;
  }
}
const store = createStore(reducer);
export default store;
```

登录成功回调函数调用` dispatch({ type: ActionTypes.USER_LOGIN, data })`

注销成功后回调`dispatch({ type: ActionTypes.EXIT })`

### redux 中的 Hook

- `useSelector` useSelector 用于从 Redux store 中提取数据, 接受函数作为参数 其中入参是整个 Redux store 的状态 返回userInfo的信息
- `useDispatch` 用于获取 Redux store 的 `dispatch` 方法
- `useStore` 用于直接访问 Redux store 实例(很少使用) 

## 配置

### TypeScript 的配置文件

配置文件 `tsconfig.json`

```json
{
  "compilerOptions": {
    // 生成的 JavaScript 代码应兼容的 ECMAScript 版本
    "target": "es5",
    // 指定要包含在编译中的库文件
    "lib": ["dom", "dom.iterable", "esnext"],
    // 允许编译 JavaScript 文件
    "allowJs": true,
    // 跳过声明文件的类型检查
    "skipLibCheck": true,
    // 启用所有严格类型检查选项
    "strict": true,
    // 不生成编译输出
    "noEmit": true,
    // 启用 esModule 互操作性, 为 CommonJS 和 AMD 模块生成相应的兼容代码
    "esModuleInterop": true,
    // 使用的模块系统, 这里是 ESNext
    "module": "esnext",
    //  决定模块解析策略, 这里是使用捆绑工具的解析策略
    "moduleResolution": "bundler",
    // 允许导入 JSON 文件
    "resolveJsonModule": true,
    // 强制每个文件成为单独的模块
    "isolatedModules": true,
    // 保留 JSX 语法(编译时不转换)
    "jsx": "preserve",
    // 启用增量编译, 能加快后续编译速度
    "incremental": true,
    "plugins": [
      {"name": "next"}
    ],
    "paths": {
      // 使用别名 "@" 代表 "./src/" 目录
      "@/*": ["./src/*"],
      // 使用别名 "@resources/" 代表 "./resources/" 目录
      "@resources/*": ["./resources/*"],
    }
  },
  // 包含在编译中的文件和目录
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  // 排除在编译之外的文件和目录
  "exclude": ["node_modules"]
}
```

``create-next-app`现在默认附带TypeScript

#### TypeScript 声明文件 (`src/types.d.ts`)

作用: 声明模块的类型, 以便在没有类型定义文件的情况下使用这些模块

那什么是`类型定义文件`

类型定义文件: 是一个用于描述 JavaScript 代码中各种变量和函数的类型的文件, 通常以 `.d.ts` 为扩展名, 并用于为 TypeScript 提供类型信息, 这使得 TypeScript 编译器能够对 JavaScript 代码进行类型检查,从而提供更好的代码补全、错误检测和文档生成

文件示例

```ts
declare module "styled-components";
declare module '*.svg' {
    const content: any;
    export default content;
}
```

### ESLint的配置

Next.js 提供了集成的ESLint, next lint作为脚本添加到package.json

运行`npm run lint`或`yarn lint`使用 ESLint 进行语法检查

`.eslintrc.json`

```json
// Strict
{
  "extends": "next/core-web-vitals"
}
```

```json
// Base
{
  "extends": "next"
}
```

或者不包括任何 ESLint 配置

### 环境变量

Next.js 内置了对环境变量的支持

`.env*` 文件用于配置环境变量

`.env`: 默认的环境变量配置文件

`.env.local`: 用于覆盖本地开发环境的变量

`.env.development`: 用于开发环境的变量

`.env.production`: 用于生产环境的变量

`.env.test`: 用于测试环境的变量

Next.js 还支持文件内的多行变量

```properties
# .env

# 单行变量
NEXT_PUBLIC_API_URL=https://api.example.com

# 多行变量
NEXT_PUBLIC_CERTIFICATE="-----BEGIN CERTIFICATE-----
MIIC+DCCAeCgAwIBAgIJAId4pO9GQz8tMA0GCSqGSIb3DQEBBQUAMIGQMQswCQYD
VQQGEwJVUzELMAkGA1UECBMCTUkxEDAOBgNVBAcTB1NhbGVtMRUwEwYDVQQKEwxU
ZXN0IENvbXBhbnkxEjAQBgNVBAsTCVRlc3QgVW5pdDENMAsGA1UEAxMEVGVzdDEh
MB8GCSqGSIb3DQEJARYSYWRtaW5AdGVzdC5jb20wHhcNMjAwNzE3MDk0MzI4WhcN
MzAwNzE1MDk0MzI4WjCBkDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAk1JMRgwFgYD
VQQHEw9EZXZlbG9wbWVudCBUZWFtMRUwEwYDVQQKEwxUZXN0IENvbXBhbnkxEjAQ
BgNVBAsTCVRlc3QgVW5pdDENMAsGA1UEAxMEVGVzdDEhMB8GCSqGSIb3DQEJARYS
YWRtaW5AdGVzdC5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAKnd50+I
HzHpCLedG3pJAkBhT4DJF8Kty5kJHpRoaxThx84LsQLkBe8ndLg6PbvzR5Ye5xT1
7NZ7tfVi2vG2uqj9VJ9Wk/ot+ML6XmF6NMWZf5JZ50A1y1a5x5zJYScpSPFUAdOr
JMj3DH27mXfKO7mBHo8PPkxmyKHzbxENo60JAgMBAAGjUDBOMB0GA1UdDgQWBBSf
3/8C/iG+P3hl6/rMr69Jvl8xPDAfBgNVHSMEGDAWgBSf3/8C/iG+P3hl6/rMr69J
vl8xPDAJBgNVHRMEAjAAMAsGA1UdDwQEAwIFoDANBgkqhkiG9w0BAQUFAAOBgQAW
EO8+c9nW8oBw5x+/KnFnVE0+TxuG6ghRVeElurv3oa6PzyxWcXilRPGO6dX8Xli8
wnZybvjlmMhpl1nJ4Otx0yZ0TyxIAnbCAVXq+EmelHqfDJqJThhtVZxpb1jQZ7DR
OH3A/Xf3OTMOWdu2Ow6hjsjK3hG8Sb54WQQ3DWWK1A==
-----END CERTIFICATE-----"
```

`多行变量`是指环境变量的值可以跨多行(需要使用适当的语法)

变量名的命名约定: 为了确保变量在客户端代码中可用, 环境变量名必须以 `NEXT_PUBLIC_` 作为前缀

加载优先级: 从高到低	

- .env.local
- .env.[environment]
- .env

引用其他变量

```properties
USER="user"
NEXT_PUBLIC_API_URL=https://api.example.com/$USER
```

### 绝对导入和模块路径别名

```tsx
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./app",
     // 设置模块路径的别名
    "paths": {
        // "@/* 映射到 ./src/
      "@/*": ["./src/*"]
    }
  }
}
```

Next.js 内置了对`paths`的`baseUrl`选项的支持(tsconfig.json/jsconfig.json文件)

`baseUrl`属性: 配置`baseUrl`选项允许您直接从项目的根目录导入(如上配置, 也可以配置其他路径作为开始路径)

`import GetEnvVars from "src/app/components/GetEnv";` ==> `import GetEnvVars from "@/app/components/GetEnv";`

注意: 上面的paths配置与baseUrl的配置相关(意思就是paths的配置是基于baseUrl开始的)

### MDX

### src 目录

出现 src 目录是为了将`应用程序代码`与`主要位于项目根目录中的项目配置`文件分开

需要注意的几个点

- /public 目录仍然需要位于根目录下
- 配置文件 `package.json`  `next.config.js` `tsconfig.json` 也是应该位于根目录下

- 环境变量文件`.env.*`也是应该位于根目录下
- 如果在你的项目根目录下同时存在 `src/app` 或 `src/pages` 目录, 并且根目录中也存在 `app` 或 `pages` 目录(没有 `src` 前缀),那么 `src/app` 或 `src/pages` 目录将会被忽略或不起作用
- 如果你选择使用 `src` 目录作为项目的源代码目录, 通常还会将其他应用程序相关的文件夹如 `/components` 或 `/lib` 等也移动到 `src` 目录下
- 如果你正在使用中间件(Middleware), 确保将它放置在项目的 `src` 目录下
- 当使用`Tailwind CSS ` 时, 配置文件`tailwind.config.js` 中content属性中需要添加src路径(参考上面的tailwind.config.js配置文件)
- 如果你在 TypeScript 项目中使用了路径别名(path alias)来进行导入, 例如 `@/components`，那么你应该更新 `tsconfig.json` 文件中的 `paths` 对象，以包含 `src/`

## Rendering

## Routing

## Authentication

## Deploying#Static Exports