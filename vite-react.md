    
## Init
```
pnpm init vite  or  yarn create vite
```

## Kea

```
yarn add reselect redux react-redux kea
yarn add -D "@types/react-redux"
```

```
import { Provider } from "react-redux";
import { getContext, resetContext } from "kea";
resetContext();

export default ()=>{
  return (
    <Provider store={getContext().store}>
      <App />
    </Provider>
  )
}

```

```
import {values, actions} from './fn/kea'

const App = () => {
    const { count } = values()
    const { setCount } = actions()

    return (
        <>
         <button onClick={() => { setCount(count + 1) }}>{count}</button>
        </>
    )
}
```

## Db

```
pnpm add dexie
```

## Router

### basic

```
pnpm add react-router-dom
```

src/routes/index.jsx
```
import React from 'react';
import { Route, Redirect, Switch } from 'react-router-dom';
import Demo from '../views/Demo/index';
import Home from '../views/Home/index';

const routerList = () => (
  <Switch>
    <Route path="/" render={() => <Redirect to="/home" />} exact key="first" />
    <Route path="/demo" component={Demo} key="demo" />
    <Route path="/home" component={Home} key="home" />
  </Switch>
);

export default routerList;
```

App.js

```
import React from 'react';
 + import Routes from './routes';
 + import { BrowserRouter } from 'react-router-dom';

import './App.css';

function App() {
  return (
   + <BrowserRouter>
   +   <Routes />
   + </BrowserRouter>
  );
}

export default App;

```

### pages router
```
yarn add -D vite-plugin-pages @vitejs/plugin-vue @vue/compiler-sfc
yarn add react-router react-router-dom react-router-config

```

for typescript
```
yarn add -D "@types/react-router-config" "@types/react-router-dom"
```
// vite-env.d.ts
/// <reference types="vite-plugin-pages/client-react" />
    "@types/react-dom"
    

Add to your vite.config.js:
```
import Vue from "@vitejs/plugin-vue"
import Pages from "vite-plugin-pages"

export default {
  plugins: [
    Vue(),
    Pages({
      react: true,
    }),
  ],
};
```

Init the routes
```
import { BrowserRouter } from "react-router-dom"
import { renderRoutes } from "react-router-config"
import routes from "virtual:generated-pages-react"

ReactDOM.render(
  <BrowserRouter>
    {renderRoutes(routes)}
  </BrowserRouter>,
  document.getElementById("root"),
);
```

pages/index.jsx(tsx)
```
import React, { useState } from 'react'
import { Link} from "react-router-dom"
import routes from "virtual:generated-pages-react"

export default () => {
    return <>
        <div>Hello</div>
        {routes.map(it => {
            return <div key={it.path as string} >
                <span>
                <Link to={it.path as string} >{it.path}</Link>
                </span>
            </div>
        })}
    </>

}
```

https://github.com/vitejs/vite-plugin-react-pages

## Test

```
yarn global add jest
pnpm add -D puppeteer
```

```
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
http://localhost:9222/json/version

const browser = await puppeteer.connect({
  browserWSEndpoint: "ws://localhost:9222/devtools/browser/9d2eb6da-ce3f-4abe-9a8b-ddc69ab8fdc0"
});
```

pnpm add -D playwright
pnpm add -D jest-puppeteer
yarn add -D jest jest-playwright-preset

baidu.test.js
```
const puppeteer = require('puppeteer')
let  browser;
let page;

beforeAll(async ()=>{
    browser = await puppeteer.connect({
        browserWSEndpoint: "ws://localhost:9222/devtools/browser/9d2eb6da-ce3f-4abe-9a8b-ddc69ab8fdc0"
    });
    page = await browser.newPage();
    page.setViewport({ width: 1920, height: 1080 })
    await page.setJavaScriptEnabled(true)
})

afterAll(async ()=>{
    await page.close();
    // await browser.close();
})

test("1. Open the baidu; 2. enter a keywords to search", async () => {
    await page.goto("https://www.baidu.com", {
        waitUntil: 'networkidle2'
    });

    const pageTitle = await page.title();
    await expect(pageTitle).toMatch('百度一下，你就知道')

    await page.focus('#kw')
    await page.type('#kw', 'test')
}, 30000)
```

## Style
```
pnpm add styled-components styled-reset
```

main.tsx
```
<React.StrictMode>
    <Reset />
</React.StrictMode>
```

## antd
```
pnpm add antd @ant-design/pro-layout
pnpm add -D less vite-plugin-imp
```

vite.config.js:
```
  plugins: [
    vitePluginImp({
      libList: [
        {
          libName: 'antd',
          style: (name) => `antd/es/${name}/style`,
          // style: (name) => [`antd/lib/style/index.less`, `antd/lib/${name}/style/index.less`],
        },
      ],
    }),
  ],
  css: {
    preprocessorOptions: {
      less: {
        javascriptEnabled: true,
      },
    }
  },
  resolve: {
    alias: [
      // fix less import by: @import ~
      // less import no support webpack alias '~' · Issue #2185 · vitejs/vite
      { find: /^~/, replacement: '' },
    ],
  },
```

## Formily

```
mkdir my-app & cd my-app
npm create @umijs/umi-app
pnpm i
pnpm add @formily/core @formily/react @formily/antd antd moment
```

## starter

### kea, pages, antd


```sh
pnpm init @vitejs/app
pnpm add react-router-dom
pnpm add reselect redux react-redux kea
pnpm add -D vite-plugin-pages @vitejs/plugin-vue @vue/compiler-sfc
pnpm add -D less vite-plugin-imp
pnpm add react-router react-router-dom react-router-config
```

## Doc

Vite2 + React + Antd 踩坑指南
https://www.jianshu.com/p/8686469a6f0e


Vite 2.x + React + Ant Design 应用开发从入门到框架实现
https://zhuanlan.zhihu.com/p/362152434
