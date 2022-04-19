课程链接：https://btholt.github.io/complete-intro-to-react-v7/



npm临时使用淘宝源

`npm --registry https://registry.npmmirror.com install [pkg-name]`



##### ESLINT

安装：

run `npm install -D eslint@8.8.0 eslint-config-prettier@8.3.0`



配置：

Create this file called `.eslintrc.json`.

```json
{
  "extends": ["eslint:recommended", "prettier"],
  "plugins": [],
  "parserOptions": {
    "ecmaVersion": 2022,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "node": true
  }
}
```



使用NPM script的技巧，传入参数的方式

- With npm scripts, you can pass additional parameters to the command if you want. Just add a `--` and then put whatever else you want to tack on after that. For example, if I wanted to get the debug output from ESLint, I could run `npm run lint -- --debug` which would translate to `eslint **/*.js --debug`.
- We can use our fix trick this way: `npm run lint -- --fix`.

---

##### Parcel

使用：

`"scripts" {  "dev": "parcel src/index.html" }`



build:

在命令行 `npx parcel build src/index.html`



serve:

在命令行 `npx serve dist`



##### Strict Mode

在return的组件上wrap 这个<React.StrictMode></React.StrictMode> 标签，会有一些warning提醒



##### React Router

安装： `npm install react-router-dom@6.2.1`

在页面上：`import { BrowserRouter, Routes, Route } from "react-router-dom";`



###### class properties

为了使用这个JS新的内容，添加了一个babel插件

安装： `npm i -D @babel/plugin-proposal-class-properties@7.16.7`

配置`.babelrc`

```json
{
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

Babel的核心概念就是插件，每一个它进行的转化都封装成了插件。

Parcel会把这个配置和它自身的配置文件合并，所以只需要添加我们需要的内容，不需要做其他修改。



###### `ErrorBoundary`

必须是 class 组件

设置static方法，会接收error并返回我们希望改变的state

通常，我们可以创建一个FallbackComponent并作为prop传递进去，当状态里有error时，return这个组件。否则，返回`this.props.children`

```javascript
class ErrorBoundary extends Component {
    state = {hasError: false, redirect: false};
    static getDerivedStateFromError() {
        return {hasError: true};
    }

    componentDidCatch(error, info) {
        console.error(error, info);
    }
....
}
```



###### Context

`createContext `是一个函数，它返回一个包含两个 React 组件的对象：一个提供者Provider和一个消费者。提供者是您确定 context 所在位置的方式。上下文仅在 Provider 内部可用。您只需执行一次。

![image-20220418200825893](C:\Users\Opsuc\AppData\Roaming\Typora\typora-user-images\image-20220418200825893.png)



