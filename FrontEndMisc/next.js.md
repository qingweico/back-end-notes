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

```
my-next-app/
├── node_modules/           // 项目所有的依赖包
├── public/                 // 静态资源,如图片、favicon 等
├── src/                    // 源代码文件
│   ├── app/
│   │   ├── favicon.ico     // 网站的图标文件
│   │   ├── globals.css     // 全局样式文件
│   │   ├── layout.tsx      // 页面的布局组件
│   │   ├── page.tsx        // 主页
│   ├── components/         // 通用组件
│   ├── hooks/              // 自定义 hook
│   ├── styles/             // 样式文件
│   ├── utils/              // 工具函数
├── .eslintrc.json          // eslint 配置文件
├── .gitignore              // git忽略文件
├── next.config.mjs         // next.js 配置文件
├── next-env.d.ts           // next.js TypeScript 声明文件
├── package.json            // 项目配置文件
├── package-lock.json       // 依赖锁定文件
├── postcss.config.mjs      // postcss 配置文件
├── README.md               // readme 文件
├── tailwind.config.ts      // Tailwind CSS 配置文件
├── tsconfig.json           // TypeScript 配置文件

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

### Defining Routes

创建路由

Next.js 使用基于文件系统的路由器，其中文件夹用于定义路由

每个文件夹代表一个映射到 URL 段的路由段, 同理若需要创建嵌套路由, 只需要将文件夹嵌套在一块

若目录下有 `page.tsx` 文件, 则可以使得该路由段公开可访问

### Pages and Layouts

#### 文件夹和文件的作用

文件夹用于定义路由

文件用于创建针对路线显示的 UI

#### 路由段

/app/user

路由中每个文件夹代表一个路由段, 每个路由段都映射到 URL 路径中的相应段

/app/user/page.tsx => 访问 app 目录下 user 目录下的 page.tsx UI 组件

#### 布局

布局是多个路由之间共享的 UI

导航时, 布局会保留状态、保持交互性、并且不会重新渲染

布局也可以嵌套

#### 文件约定

`layout	`          共享页面

`page`            公共可访问的路由页面

`loading`         加载页面

`not-found`       404页面

`error`           错误页面

`global-error`    全局错误页面

`route `           服务器端API端点

`template`        专门的重新渲染布局页面

`default`         并行路由回退的页面

#### 组件层次结构

- `layout.js`
- `template.js`
- `error.js`
- `loading.js`
- `not-found.js`
- `page.js`或者嵌套`layout.js`

#### 嵌套路由

默认情况下,文件夹层次结构中的布局是嵌套的, 所以在其他目录下增加`layout.js` 文件来增加布局嵌套

总结:

- 只有根布局可以包含`<html>`和`<body>`标签, 其他目录下的`layout.js` 若包含则会报错
- 当`layout.js`和`page.js`文件在同一个文件夹中定义时, 布局将包装页面
- 无法在父布局及其子布局之间传递数据
- Next.js 的一个 React Hook `useSelectedLayoutSegment` `useSelectedLayoutSegments`
    - 前者在布局组件中获取当前选中的布局段
    - 后者在布局组件中获取当前选中的所有布局段
- 使用 Route Group 来选择将特定路线段加入或退出共享布局 ()
- 使用 Route Group 来创建一个多个的根布局

#### 高级路由模式

- 并行路由 在同一个视图中同时显示两个或者多个可独立导航的页面
- 拦截路线 拦截一个路由并将其显示在另一个路由的上下文中

#### 模板

在 Next.js 中 模板 template.js 是用于定义布局和组件模板的方式, 通常与通常与 App Router 一起使用, 创建可重用的布局和页面结构,使得在不同页面之间共享相同的布局

template.js 和 layout.js 的区别

- layout.js: 定义全局布局, 包含在所有页面上共享的组件和结构
- template.js 定义可重用的模板结构, 可以在特定页面或一组页面之间共享布局和样式

#### 元数据

在 app 目录下 可以使用 `Metadata ` API 去修改 html 中的 head 标签中的元素, 比如 `title`

```tsx
import { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: 'Next.js',
}
 
export default function Page() {
  return '...'
}
```

使用函数 `generateMetadata` 来定义元数据, 在 Next.js13中加入的新功能,可以为页面或布局动态生成元数据

```tsx
export async function generateMetadata() {
   
    return {
        title: {
            default: 'My Next.js App',
            template: '%s | My Next.js App',
        },
        description: 'This is the description for the entire app',
    };
}
```

tips: 不应该手动添加 <head> 标签, 比如<titile> <meta> 到根标签中

### Linking and Navigating

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

### Loading UI 和 Streaming

`loading.js`通过在文件夹内添加文件来创建加载状态

在同一个文件夹下 loading.js 将嵌套在 layout.js

React.FC是 TypeScript 中用于定义函数组件的类型别名

作用: 为函数组件提供类型检查和自动完成功能

示例:

```tsx
import React from "react";

interface PowerProps {
    speed: number;
    strength: number;
}

const PlayerPower: React.FC<PowerProps> = ({speed, strength}) => {

    return (
        <>
            <h3>Player Power</h3>
            <p>{speed}</p>
            <p>{strength}</p>
        </>
    )
}

export default PlayerPower
```

```tsx
// page.tsx
 <PlayerPower speed={2000} strength={3000}/>
```

### 使用 Suspense 进行流式传输

#### 什么是`<Suspense>`

`<Suspense>`是 React 提供的一个组件, 用于优化代码分割和异步加载组件时的用户体验

工作原理: 包装一个执行异步操作的组件, 在操作发生时显示其fallback,操作完成后替换之

主要作用是等待异步操作完成期间,渲染备用内容或显示加载指示器, 以提升页面的交互性和用户感知

示例: 处理模拟加载状态, 使得在组件加载完成之前能够显示备用内容或加载指示器

```tsx
import React, { Suspense } from 'react';
const simulateDelay = (ms: number) => new Promise(resolve => setTimeout(resolve, ms));
// 延迟加载组件
const LazyComponent =  React.lazy(() =>
    simulateDelay(2000).then(() => import('@/app/render/LazyComponent'))
);
export default function LoadingPage() {
    return (
        <Suspense fallback={<div>Loading...</div>}>
            <LazyComponent />
        </Suspense>
    );
}
```

App Router 支持使用 Suspense 进行流式传输

服务器渲染(SSR)

- 服务器会获取指定页面的所有数据
- 服务器呈现指定页面的 HTML
- 页面的 HTML CSS和 JavaScript将被发送到客户端
- 使用生成的 HTML 和 CSS 先生成没有交互逻辑的用户界面
- 使用 React hydrate 用户界面, 使之具有交互逻辑

存在的问题: 上述步骤是连续且阻塞的,只有在获取所有数据后才能呈现页面的 HTML, 而且只有客户端下载所有的组件后,才可以进行hydrate

虽然 React 和 Next.js 使用 SSR 技术, 提高了 UI 界面的加载性能, 可以让用户直接的感受到页面的渲染速度提升,但是在显示页面 UI 之前, 还是需要完成服务器上所有数据的获取

解决: 流式传输, 将页面的 HTML 分解为更小的块, 分批发送这些块, 从服务器到客户端

优先级较高的组件或不依赖数据的组件可以先发送, 发送到客户端后, React 可以尽快的 hydrate(害醉特 水合), 同理, 优先级较低的组件可以在其数据被获取后在同一服务器请求中发送

使用意义:

- 从服务器逐步将 HTML 渲染到客户端
- 选择性hydrate, React 根据用户交互确定首先使哪些组件具有交互的优先级

### Error Handling

同样在文件目录下创建`error.js` 文件(注意:错误处理组件需要声明为客户端组件)

工作原理

`<ErrorBoundary>` 是 React 中的一个特殊组件, 用于捕获并处理子组件中发生的 JavaScript 错误,从而防止整个应用崩溃

当子组件中的 JavaScript 错误未被捕获时, React 会向上遍历组件树, 直到找到最近的 `<ErrorBoundary>` 组件, 并触发它的错误处理机制

-  error.js 文件会自动创建<ErrorBoundary>, 并包含其嵌套的child segment或者page.js组件
-  导出error.js的 React 组件作为 fallback 组件
-  当错误在<ErrorBoundary>范围内发生时, 则将该错误呈现在 fallback  组件内
-  当 fallback 组件处于活动状态时, <ErrorBoundary> 上方的布局可以仍然保持其状态和交互能力, 且 fallback 组件可以显示地从错误中恢复

#### Recovering From Errors(从错误中回复)

fallback 组件使用 `reset()` 函数提示用户尝试从错误中恢复, 该函数执行,将尝试重新渲染<ErrorBoundary>范围内的内容, 如果成功, fallback 组件将替换为重新渲染的结果

#### 处理根布局的错误

app 文件夹下 `error.js`文件不会捕获app/layout.js或者app/template.js组件中引发的错误, 需要使用global-error.js且需要包含在 html 和 body 标签中,(这个是捕获范围最大异常处理, 相当于 Exception)

#### 处理服务器错误

服务器内部抛出错误, 则会将一个 Error 对象作为 error prop 转发到最近的文件

为了保护敏感错误信息, Error 转发给客户端的对象只是包含一个通用的 `message` 和 `digest` 属性[server error](https://nextjs.org/docs/app/building-your-application/routing/error-handling#handling-server-errors)

### Redirecting(重定向)

#### redirect

将用户重定向到另一个 URL

- redirect 默认返回 307(临时重定向)状态代码, 在服务器中操作时会返回303(See Other)

```
什么是307(Temporary Redirect)

告诉客户端所请求的资源暂时被移动到了另一个 URL,但客户端在将来的请求中仍应使用原始的 URL
与302 重定向不同的是,307 状态码要求客户端在重定向请求时保持请求方法和请求体不变

- 临时性, 资源只是暂时移动到另一个位置
- 请求方法保持不变, 如果原始请求是 POST 请求，重定向后的请求也必须是 POST 请求,与 302 状态码不同, 后者在重定向时可能会将 POST 请求变为 GET 请求
- 请求体保持不变, 如果原始请求有请求体, 重定向后的请求必须包含相同的请求体

什么是303(See Other)

告诉客户端请求的资源可以在另一个 URL 处找到, 通常用于将客户端重定向到另一个资源,303 状态码明确指示客户端应该使用 GET 方法进行重定向请求,即使原始请求是 POST

具体含义

- 临时性, 资源只是暂时可以在另一个 URL 处找到
- 请求方法变为 GET 无论原来请求使用的那种方法, 重定向后的请求都会使用 GET 方法

使用场景

- 表单提交后重定向
- 资源创建后重定向
```

- `redirect`内部会引发错误, 所以应该使用 try/catch 包围
- `redirect` 可以在渲染过程中在客户端组件中调用, 但不能在事件处理程序中调用, 如果想在客户端使用, 请使用`useRouter`
- `redirect`可以接受绝对url已访问外部链接
- 如果想在渲染之前使用重定向, 可以使用`next.config.js`或者`中间件`

#### permanentRedirect

将用户永久重定向到另一个 URL,在服务器端设置 HTTP 响应,告知客户端进行永久重定向到指定的 URL,即使客户端再次访问原始 URL,也会直接跳转到新的 URL

- 默认返回 308 状态代码
- 接受绝对 URL 并可用于重定向到外部链接
- 如果想在渲染之前使用重定向, 可以使用`next.config.js`或者`中间件`

308 和 301 状态码的区别

#### useRouter

需要在客户端组件的事件处理程序内进行重定向, 可以使用 useRouter 函数(编程式路由), 如果不需要编程式导航, 可以使用组件<Link>

#### 配置文件 `next.config.js` 中的 redirects

在next.config.js中可以配置redirects选项将传入的请求路径重定向到不同的目标路径,支持 path, header,cookie,query matching

示例配置

```ts
module.exports = {
  async redirects() {
    return [
      {
        source: '/index',
        destination: '/',
        permanent: true,
      },
      // Wildcard path matching
      {
        source: '/post/:id',
        destination: '/art/:id',
        permanent: true,
      },
    ]
  },
}
```

- redirects 可以选择返回 307(临时重定向) 或者 308(永久重定向)
- redirects 重定向的次数会受平台的限制
- redirects 会在中间件之前运行

#### NextResponse.redirect

主要使用: 手动调用 `NextResponse.next()` 来控制请求和响应的流程, 在请求完成之前运行代码, 主要用于身份验证、会话管理等场景, 如果请求某个需要用户登录后才可以访问的url, 如果未登录,则重定向到登陆页面

### Route Groups(路由组)

目的

- 避免实际的文件路径目录影响到URL路径结构
- 可以根据不同的分类需求对路由结构进行划分
- 在同一路由段下的页面可以共享 layout, 也可以创建多个根布局(先将顶层的layout删除,在向每个路由组内添加layout)

### Project Organization and File Colocation(项目组织和文件配置)

只有添加 page.js 文件到目录下, 该路由段才是可公开访问的, 且只有返回的内容才会被发送到客户端, 该目录下的其他文件则不会被访问

- 与 pages route 不同的是, 后者目录中的任何文件都会被视为路由
- 除了路由文件夹和文件约定外, 对其他的文件放置没有做规定

#### 私人文件夹

`_folderName` 该文件夹及其所有子文件夹排除在路由外

作用

- 将UI逻辑和路由逻辑分离

### src 目录

目的: 将应用程序代码与项目配置文件(主要位于项目的根目录中)分开

### 项目目录组织策略

- 应用程序代码放置在项目根目录下, app 目录只用作路由
- 将所有应用程序代码存储在app目录下
- 按照功能拆分到对应的路由段中

### Dynamic Routes(动态路由)

### Parallel Routes(并行路由)

路由组的区别

允许同时或有条件地在同一布局中渲染一个或多个页面

并行路由使用命名插槽创建, `@folder`

- 插槽不是路由段, 因此不会影响 URL 结构

### Intercepting Routes(拦截路由)

### Route Handlers(路由处理器)

### Middleware(中间件)

中间件在请求完成之前运行代码, 达到根据传入的请求, 通过重写、重定向、修改请求或响应标头或直接响应来修改响应的目的

中间件在缓存内容和路由匹配之前运行、

主要使用场景

- 身份验证和授权
- 服务器端重定向
- 路径重写
- 机器人检测
- 日志和分析
- 功能标记

应该避免的操作

- 复杂的数据获取和操作
- 繁重的计算任务
- 广泛的会话管理
- 直接数据库操作

在配置文件 `middleware.ts` 中编写配置项, 可以放在根目录下也可放置在src目录下

#### 路径匹配

因为项目中的每条路由都会调用中间件

##### 匹配器

- `matcher`允许过滤中间件在特定路径上运行

```tsx
export const config = {
    // 可以使用数组语法匹配单个路径或者多个路径
  matcher: ['/about/:path*', '/dashboard/:path*'],
}
```

- `matcher`允许使用完整的正则表达式
- 可以使用`missing`或`has`数组, 或者两者结合

注意的点

- 匹配的路径必须以/开头
- 可以包含命名参数, 例如 `/about/:path` 中的 :path
- 命名参数上可以有多个修饰符(以:开头), 其中`*`为0个或者多个, `?`为零个或者一个, `+`为一个或者多个

##### 条件语句

示例

```tsx
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
 
export function middleware(request: NextRequest) {
  if (request.nextUrl.pathname.startsWith('/index')) {
    return NextResponse.rewrite(new URL('/', request.url))
  }
}
```

#### NextResponse API

通过 NextResponse  可以实现的功能

- 将传入的请求转到另外一个 URL
- 通过显示给定的 URL 来获取响应
- 设置API路由、`getServerSideProps`, `rewrite`目标的请求头
- 设置响应 cookie
- 设置响应头

如何在中间件中生成 NextResponse

- rewrite 一个可以产生 response 的路由
- 直接返回一个 NextResponse

### i18n

Next.js 提供了内置的 i18n 支持, 可以在 配置文件 `next.config.js` 中配置

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

## Rendering(渲染)

### 服务器组件

/app/user

/app/user/:id

`部分渲染`

渲染可以根据`路由段`拆分 以实现`流式渲染`和`部分渲染`

服务器渲染的好处

- **数据获取**  减少了获取数据的时间 ； 以及客户端需要发出请求的数量
- **安全性**   减少了将敏感数据直接暴露给客户端的风险
- **缓存**     缓存结果 减少请求
- **性能**     在使用完全由客户端组件组成的应用时，减少客户端需要渲染的JavaScript脚本的数量
- **初始页面加载和首次内容绘制(FCP)**  在服务器端 进行HTML 的渲染 减少用户等待的时间
- **搜索引擎优化和社交网络可共享性**    便于搜索引擎根据呈现的 HTML 进行索引
- **流式传输** 可以将服务器组件拆分为多个块进行渲染并将结果流式传输至客户端 无需等待整个页面全部在服务器上渲染完成

默认情况下, Next.js 使用服务器组件, 无需额外的配置

服务器组件的渲染呈现

- React 将服务器组件渲染为 React 服务器组件负载(RSC Payload) 这种特殊的数据格式
- Next.js 使用 RSC Payload 和 客户端组件 JavaScript 指令在服务器上呈现 HTML

客户端上

- HTML 在初始加载时用于显示只用于展示非交互式的界面框架
- React Server Components Payload 用于协调客户端和服务器组件树 ; 更新 DOM
- JavaScript 指令用于 [hydrateRoot](https://react.dev/reference/react-dom/client/hydrateRoot) 客户端组件并使程序具有交互性

什么是 RSC Payload(React 服务器组件有效负载)

渲染的React Server Components树的紧凑二进制表示, React 在客户端使用它来更新浏览器的 DOM

- 服务器组件的渲染结果
- 客户端组件应呈现的位置的占位符以及对其 JavaScript 组件的应用
- 从服务器组件传递到客户端组件的任何 props [参见原链接](https://nextjs.org/docs/app/building-your-application/rendering/composition-patterns#supported-pattern-passing-server-components-to-client-components-as-props)

服务器渲染策略

- 静态 默认; 路由在构建时渲染 或在数据重新验证后在后台渲染; 结果被缓存并可以推送到 CDN
- 动态 可以在请求时为每个用户渲染路由
- 流式

带有缓存数据的动态路由

切换到动态渲染

| 动态函数 | 数据   | 路由     |
| -------- | ------ | -------- |
| 否       | 已缓存 | 静态渲染 |
| 是       | 已缓存 | 动态渲染 |
| 否       | 未缓存 | 动态渲染 |
| 是       | 未缓存 | 动态渲染 |

Next.js 会根据所使用的功能和 API 自动为每条路由选择最佳渲染策略

可以选择何时缓存或重新验证特定数据 并且可以选择流式传输UI的各个部分

什么是动态函数

动态函数依赖于只能在请求时知道的信息

- **[`cookies()`](https://nextjs.org/docs/app/api-reference/functions/cookies)**

- **[`headers()`](https://nextjs.org/docs/app/api-reference/functions/headers)**
- **[`searchParams`](https://nextjs.org/docs/app/api-reference/file-conventions/page#searchparams-optional)**

流式传输

默认情况下 流式传输内置于 Next.js 的应用路由中(Streaming is built into the Next.js)

### 客户端组件

客户端渲染的好处

- 交互性 可以向用户提供即时反馈并更新UI
- 浏览器API 可以访问浏览器的API

使用客户端组件

在组件文件顶部显示使用`use client`显示使用客户端组件(导入到其中的所有其他模块 包括子组件, 都被视为客户端组件的一部分)

客户端的渲染加载: 参考上面的服务器组件渲染

什么是hydrate

将事件监听器附加到 DOM 的过程, 以使静态 HTML 具有交互性, 通过 API `hydrateRoot`实现

### 服务器和客户端组合模式

|                        动作                         | 服务器组件 | 客户端组件 |
| :-------------------------------------------------: | :--------: | :--------: |
|                      获取数据                       |    支持    |   不支持   |
|                  直接访问后端资源                   |    支持    |   不支持   |
|     在服务器上保存敏感信息(访问令牌、 API密钥)      |    支持    |   不支持   |
|                减少客户端 JavaScript                |    支持    |   不支持   |
|             交互性的事件监听器(onClick)             |   不支持   |    支持    |
|               use开头的 Hook 钩子函数               |   不支持   |    支持    |
|                   使用浏览器的API                   |   不支持   |    支持    |
| 使用依赖于状态、效果或仅限浏览器的 API 的自定义钩子 |   不支持   |    支持    |
|                   ReactClass组件                    |   不支持   |    支持    |

使用服务器组件的场景

- 不同组件之间共享数据, 不使用 React Context, 或将数据作为 props 传递(React Context 在服务器上不可用)
- 将仅使用在服务器的代码排除在客户端环境之外

由于 JavaScript 模块可以在服务器和客户端组件模块之间共享, 所以之前只准备在服务器上运行的代码可能会在客户端上运行

环境变量中若带有前缀`NEXT_PUBLIC`,则该变量只能在服务器上访问的私有变量(为了防止环境变量泄露给客户端, Nextjs会将私有变量替换成空字符串), 为了避免如上情况,可以通过使用`server-only`, 通过使用`import 'server-only'` 来说明包含仅限服务器代码的模块,若出现客户端代码则在构建时出现错误

`client-only` 同理

优化: 将客户端组件沿树向下移动

为了减少客户端 JavaScript 包的大小, 可以将一些静态元素移动至服务端, 而保留那些客户端支持的组件内容(比如一些交互事件)

将 props 从服务器传递到客户端组件[序列化](https://react.dev/reference/rsc/use-server#serializable-parameters-and-return-values)

交错服务器和客户端组件

在客户端子树中, 可以嵌套服务器组件或调用服务器操作

- 如果你需要在客户端上访问服务器上的数据或资源, 需要向服务器发出新的请求 而不是在客户端组件和服务器组件之间来回切换
- 由于客户端组件是在服务器组件之后呈现的, 所以无法将服务器组件导入客户端组件模块(为什么需要向服务器发出新的请求), 可以将服务器组件作为 Props 传递给客户端组件

### Edge 和 Node.js 运行时

在 Next.js 中上下文中, 运行时是指在代码执行期间可用的一组库、API和常规功能

在服务器上, 有两个运行时可以用来呈现应用程序代码的各个部分

- Node.js 运行时(默认)
- Edge Runtime 基于 Web API(是前者的子集)

## Authentication

## Deploying#Static Exports(静态导出)

运行时`next build` Next.js 会为每个路由生成一个 HTML 文件

通过将严格的 SPA(单页应用程序) 分解为单独的 HTML 文件, Next.js可以避免在客户端加载不必要的 JavaScript代码, 从而减少包大小并加快页面加载速度

启用静态导出

```js
/**
 * @type {import('next').NextConfig}
 */
const nextConfig = {
  output: 'export'
}
 
module.exports = nextConfig
```

支持的功能: Next.js的核心已被设计为支持静态导出

#### 服务器组件

使用静态导出时, app 目录内使用的服务器组件将在构建期间运行, 服务器组件无需进行任何修改(除非使用动态服务器函数)

#### 客户端组件

如果要在客户端执行数据获取, 可以使用 React 的 Hook 函数 `useSWR` 记住请求

什么是`SWR`

stale-while-revalidate,是 HTTP 缓存中的一种策略, `useSWR`提供了一种简洁和强大的方式来处理数据获取、缓存和同步等任务

安装 `npm install swr`  使用 `import useSWR from 'swr'`

示例

```tsx
'use client'
 
import useSWR from 'swr'
 
const fetcher = (url: string) => fetch(url).then((r) => r.json())
 
export default function Page() {
  const { data, error } = useSWR(
    `https://jsonplaceholder.typicode.com/posts/1`,
    fetcher
  )
  if (error) return 'Failed to load'
  if (!data) return 'Loading...'
 
  return data.title
}
```

路由转换发生在客户端

#### 图像优化

可以自定义图像加载器

```js
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
  images: {
    loader: 'custom',
    loaderFile: './my-loader.ts',
  },
}
 
module.exports = nextConfig
```

```ts
// my-loader.ts
export default function cloudinaryLoader({
  src,
  width,
  quality,
}: {
  src: string
  width: number
  quality?: number
}) {
  const params = ['f_auto', 'c_limit', `w_${width}`, `q_${quality || 'auto'}`]
  return `https://res.cloudinary.com/demo/image/upload/${params.join(
    ','
  )}${src}`
}
// 使用<Image> 标签时传入的src会替换成相对路径
```

#### 路由处理

```ts
// 生成data.json包含{ name: 'Lee' }
export async function GET() {
  return Response.json({ name: 'Lee' })
}
```

上述示例会在运行时呈现成静态数据, 仅 GET 方法, 可以从缓存或非缓存数据生成静态 HTML, JSON, TXT 或其它文件

tip: 需要从传入的请求中读取动态值, 则不能使用静态导出

#### 浏览器 API

客户端组件在运行期间预呈现为 HTML, Web API 等 API 不可在服务器上使用

#### 不支持的功能

不支持需要Node.js服务器的功能, 或者在构建过程中无法计算的动态逻辑

- 动态路由 带有 dynamicParams: true
- 动态路由 不带有 generateStaticParams()
- Route Handlers
- Cookies
- Rewrites
- Redirects
- Headers
- Middleware
- Incremental Static Regeneration
- Image Optimization 使用默认的 `loader`
- Draft Mode

## React 中的性能优化

### shallowEqual

函数通常用于比较两个对象或值在浅层次上是否相等

通常和一下函数结合使用

- React.memo

- useMemo

- useSelector

示例:

React 组件的渲染机制

- 当组件的状态或者 props 发生变化时, 会重新执行组件函数
- 如果组件的父组件发生重新渲染,子组件也会跟着重新渲染

Redux 状态的变化并不一定会直接触发 React 组件的重新渲染, 只有在被映射到组件的 props 或者 state 上，并且导致了实际的 props 或者 state 变化时，才会触发 React 组件的重新渲染

