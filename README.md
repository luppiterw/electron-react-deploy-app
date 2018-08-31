## 概述
笔者目前在使用Electron开发跨平台桌面应用，基于原生的GUI库的支持有限，需要使用React辅助开发，之前未开发过前端项目，这次踩的坑也很多，现将对应部署过程记录，以供后来才考。

## 开发环境
笔者通常在MacOS/Windows上同步验证：
|软件|版本|说明|
|-----|-----|-----|
|MacOS|10.13.6|macOS High Sierra|
|Windows|Windows7专业版|-|
|node|10.9.0|Mac上使用brew安装|
|npm|mac-6.2.0、windows-6.4.1|-|
|electron|2.0.8|npm install 安装|
|create-react-app|1.5.2|npm install安装|
|react|16.4.2|npm install安装|
|yarn|1.9.4|npm install安装|
|electron-packager|12.1.1|npm install安装|

## React环境部署及应用测试
facebook提供了 [create-react-app（github地址）](https://github.com/facebook/create-react-app) 用于快捷部署react开发环境，使用 npx指令（**需要npm 5.2+及更高版本**）即可快速部署：
```
npx create-react-app my-app
cd my-app
npm start
```
主要输出内容如下：
> Creating a new React app in
> /XXXX/my-app.
> 
> Installing packages. This might take a couple of minutes. Installing
> react, react-dom, and react-scripts...
> 
> yarn add v1.9.4 info No lockfile found. [1/4] 🔍  
> Resolving packages... [2/4] 🚚  
> Fetching packages... [3/4] 🔗  Linking dependencies... 
> [4/4] 📃  Building fresh packages... success Saved
> lockfile. success Saved 765 new dependencies. info Direct dependencies
> ├─ react-dom@16.4.2 ├─ react-scripts@1.1.5 └─ react@16.4.2 info All
> dependencies ✨  Done in 36.93s.
> 
> Success! Created my-app at
> /XXXX/my-app Inside that directory,
> you can run several commands:
> 
>   yarn start
>     Starts the development server.
> 
>   yarn build
>     Bundles the app into static files for production.
> 
>   yarn test
>     Starts the test runner.
> 
>   yarn eject
>     Removes this tool and copies build dependencies, configuration files
>     and scripts into the app directory. If you do this, you can’t go back!
> 
> We suggest that you begin by typing:
> 
>   cd my-app   yarn start
> 
> Happy hacking!

生成的代码目录如下：
<center>![my-app代码目录](https://img-blog.csdn.net/20180831100445618?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Zpdmlzb3Vs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)</center>

按照提示，执行 ***yarn-start***，输出如下：

> Compiled successfully!
> 
> You can now view my-app in the browser.
> 
>   Local:            http://localhost:3000/   On Your Network: 
> http://10.10.20.77:3000/
> 
> Note that the development build is not optimized. To create a
> production build, use yarn build.

浏览器中自动打开如下页面：
<center>![my-app-react-首页](https://img-blog.csdn.net/20180831095641794?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Zpdmlzb3Vs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)</center>
当需要发布应用时，需要运行 ***npm run build*** 生成build目录：

> > my-app@0.1.0 build /XXXX/my-app
> > react-scripts build
> 
> Creating an optimized production build... Compiled successfully.
> 
> File sizes after gzip:
> 
>   36.94 KB  build/static/js/main.a285be49.js   299 B     build/static/css/main.c17080f1.css
> 
> The project was built assuming it is hosted at the server root. You
> can control this with the homepage field in your package.json. For
> example, add this to build it for GitHub Pages:
> 
>   "homepage" : "http://myname.github.io/myapp",
> 
> The build folder is ready to be deployed. You may serve it with a
> static server:
> 
>   yarn global add serve   serve -s build
> 
> Find out more about deployment here:
> 
>   http://bit.ly/2vY88Kr

目录内容如下：
<center>![my-app build目录](https://img-blog.csdn.net/20180831100137810?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Zpdmlzb3Vs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)</center>

该发布是为了后续打包成桌面应用使用，本文旨在描述Electron的使用，将在后面章节描述对应使用。

## Electron环境部署及应用测试
在my-app下安装electron模块：
```
npm install -save electron
```
在my-app目录下创建main.js入口文件，对应代码参考[React + Electron 搭建一个桌面应用](https://juejin.im/post/5a6a91276fb9a01cbd58ce32) ：

```
// 引入electron并创建一个Browserwindow
const {app, BrowserWindow} = require('electron')
const path = require('path')
const url = require('url')

// 保持window对象的全局引用,避免JavaScript对象被垃圾回收时,窗口被自动关闭.
let mainWindow

function createWindow () {
//创建浏览器窗口,宽高自定义具体大小你开心就好
mainWindow = new BrowserWindow({width: 800, height: 600})

  /* 
   * 加载应用-----  electron-quick-start中默认的加载入口
    mainWindow.loadURL(url.format({
      pathname: path.join(__dirname, 'index.html'),
      protocol: 'file:',
      slashes: true
    }))
  */
  // 加载应用----适用于 react 项目
  mainWindow.loadURL('http://localhost:3000/');
  
  // 打开开发者工具，默认不打开
  // mainWindow.webContents.openDevTools()

  // 关闭window时触发下列事件.
  mainWindow.on('closed', function () {
    mainWindow = null
  })
}

// 当 Electron 完成初始化并准备创建浏览器窗口时调用此方法
app.on('ready', createWindow)

// 所有窗口关闭时退出应用.
app.on('window-all-closed', function () {
  // macOS中除非用户按下 `Cmd + Q` 显式退出,否则应用与菜单栏始终处于活动状态.
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', function () {
   // macOS中点击Dock图标时没有已打开的其余应用窗口时,则通常在应用中重建一个窗口
  if (mainWindow === null) {
    createWindow()
  }
})

// 你可以在这个脚本中续写或者使用require引入独立的js文件.   
```
**package.json** 文件中添加两个配置属性：

```
 "main": "main.js",
 "homepage": ".",
```
**启动应用注意点**：
1、需要使用 ***npm start*** 先行开启react服务，使用命令后，如浏览器自动跳转，则会进入http://localhost:3000/页面。
2、然后使用 ***electron .***  运行Electron应用（也可以在package.json中配置electron的运行脚本，但实际还是调用 ***electron .*** 指令。

**另一种直接启动应用的方式**：
1、使用 ***npm run build*** 指令，生成（更新）build目录文件。
2、将 main.js 中的 *mainWindow.loadURL('http://localhost:3000/')*;  修改为  
```
mainWindow.loadURL(url.format({
    pathname: path.join(__dirname, './build/index.html'),
    protocol: 'file:',
    slashes: true
  }));
```
上述 **./build/index.html** 即为使用 ***npm run build*** 之后的页面索引路径。
3、直接运行 ***electron .*** 指令。
<center>![my-app-electron运行](https://img-blog.csdn.net/20180831104152866?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Zpdmlzb3Vs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)</center>

第二种方式将会在后续打包Electron中使用。


## Electron应用打包
正如[React + Electron 搭建一个桌面应用](https://juejin.im/post/5a6a91276fb9a01cbd58ce32)中描述的：

> 默认情况下，homepage 是 http://localhost:3000，build 后，所有资源文件路径都是 /static，而
> Electron 调用的入口是 file :协议，/static 就会定位到根目录去，所以找不到静态文件。在 package.json
> 文件中添加 homepage 字段并设置为"."后，静态文件的路径就变成了相对路径，就能正确地找到了添加如下配置：
> "homepage":"."

上一节中讲到的第二种执行方式，理论上需要每次修改之后都执行一遍 ***npm run build*** 才能得到正确的结果。

打包工具electron-packager的一般语法为：

> ***electron-packager <应用目录> <应用名称> <打包平台> --out <输出目录> <架构> <应用版本>***

以 **my-app** 项目，在my-app目录下，输入如下：

> ***electron-packager .  my-app --darwin --out out_dir --arch=x64  --electron-version=2.0.8***

输出结果如下：

> Packaging app for platform darwin x64 using electron v2.0.8 WARNING:
> Found 'electron' but not as a devDependency, pruning anyway Wrote new
> app to out_dir/my-app-darwin-x64

上述指定Mac平台为***--darwin***，64位指定***--arch=x64***即可。
生成了一个 ***out_dir/my-app-darwin-x64*** 目录。
<center>![my-app-darwin-x64打包目录](https://img-blog.csdn.net/20180831110314982?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Zpdmlzb3Vs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)</center>

运行对应的 **my-app.app** ：
![my-app-打包后-darwin-运行app](https://img-blog.csdn.net/20180831110424319?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Zpdmlzb3Vs/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
**成功！**

## Github源码
本文所有代码可参考 [Github-electron-deploy-app](https://github.com/whvirgil2015/electron-react-deploy-app ) 。

## 参考链接

 - [facebook/create-react-app](https://github.com/facebook/create-react-app)
 - [kitze/react-electron-example](https://github.com/kitze/react-electron-example)
 - [Electron 开发桌面应用（一） 编写→运行→打包](https://blog.csdn.net/yu17310133443/article/details/79494953)
 - [React + Electron 搭建一个桌面应用](https://juejin.im/post/5a6a91276fb9a01cbd58ce32)
 - [How to Start Building Desktop Apps with Electron and React](https://blog.alexdevero.com/building-desktop-apps-electron-react/)
 - [Takeaways on Building a React-Based App With Electron](https://dzone.com/articles/takeaways-on-building-a-react-based-app-with-elect-1)
 - [CREATE AN APP WITH ELECTRON AND REACT](https://flaviocopes.com/react-electron/)
 - [electron-userland/electron-packager](https://github.com/electron-userland/electron-packager)
