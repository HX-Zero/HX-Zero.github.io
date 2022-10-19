# 实用！Cocos Creator 游戏发布 Steam 平台攻略出炉

近日，一名来自巴西圣保罗的游戏开发者 Danilo Ganzella 将他的游戏《Wirewalk()↳》通过 Cocos Creator 导出发布至全球最大的 PC 游戏平台 Steam。

![img](https://filescdn.proginn.com/721a296232b51d092ef20f6c1d76f7b4/142167cf12f5fa85131eb00b040d9217.webp)

> 游戏链接：
> https://store.steampowered.com/app/1636700/Wirewalk/

Danilo 的解决方案是**通过使用 `Node.js` 来实现，所使用到的工具包括 `Electron` 和 `Greenworks`**。基于 `Electron`，可以轻松将 Cocos Creator 发布到任何桌面。

征得 Danilo 同意之后，我们将他的解决方案翻译整理成本文。如何你的游戏是采用 Cocos Creator 开发制作并希望发布为桌面应用，这篇文章将给予你帮助。

> 注：本文所有命令行都将从 `Windows VSCode` 终端或 `Windows Powershell` 运行。

------





1

**了解 Node.js** 

`Node.js` 是一个功能强大的工具，通过使用 `JavaScript` 作为通用语言部署到许多原生平台，可以开发多种类型的应用程序和其他工具。

#  

2

 **安装 Electron** 

1.安装 `Node.js`（请选择最新的稳定版本）

2.创建一个 `Electron` 项目，打开 `VSCode`，找到你想要的项目文件夹，然后输入：

```
npm init -y
npm i --save-dev electron
```

在同一个文件夹中创建一个名为 `index.js` 的文件，内容如下：

```
const { app, BrowserWindow } = require('electron')

app.on('window-all-closed', function() {
  if (process.platform != 'darwin')
    app.quit();
});

app.on('ready', function() {

    mainWindow = new BrowserWindow({
      width: 768,
      height: 768,
      show: true,
      fullscreen: false,
      resizable: false,
      frame: true,
      title: "MyGame"
    });

    mainWindow.removeMenu();

    mainWindow.on('closed', function() {
      mainWindow = null;
    });
});
```

可以通过执行来测试它。

```
electron .
```

如果它打开一个标题为`MyGame`的空白应用程序窗口，说明你已完成。

现在我们需要将 Cocos 导出的项目提供给 `BrowserWindow`。

> 了解更多创建 Electrom 信息：https://www.electronjs.org/docs/tutorial/quick-start#create-the-main-script-file

# 将项目从 Creator 导出到 Electron

打开你的 Cocos Creator 项目，选择你希望将其导出到 `Electron` 项目中的某处，在 `Electron` 项目的根文件夹中创建一个名为`cocosExport`的子文件夹。

打开导出的项目并运行游戏：

回到 `Electron` 的 `index.js`，你要做的是在创建 `mainWindow` 对象后在任何地方添加：

```
    mainWindow.loadURL('file://' + __dirname + '/cocosExport/web-desktop/index.html').then(() =>{
    });
```

现在简单地执行它，游戏就可以运行了！

```
electron .
```

你可能需要对从 Cocos 导出的 `HTML` 以及 `Electron` 的 `BrowserWindow` 进行额外调整，使其看起来更流畅。接下来，讲一下如何集成 Steamworks API。

```
GameDiv 设置css属性
min-width: 100vw;
min-height: 100vh;
```

# 安装 Greenworks 和 Steamworks API

由于 `Steamworks` 本身不支持 `Electron`（甚至在 `JavaScript` 中也不支持），因此你需要下载一个名为 `Greenworks` 的工具。这个工具也是一个 `Node.js` 包，它通过在 `JavaScript` 中暴露一个接口来访问 `Steam API` 的本地编译的 `C++`函数。

### 下载 Greenworks 和 Steamworks API

下载 `Greenworks` 存储库的主分支并将其放在单独的文件夹中。

从 Steam 开发者门户下载 `Steamworks API`，将 `Steamworks API` 放在 `Greenworks` 根目录下的 `deps` 文件夹中，并将 `Steamworks` 文件夹重命名为`steamworks_sdk`。我们需要构建 `Greenworks`，以便二进制文件在你下载并已安装的 `Node.js` 版本上正常运行。

### 构建 Steamworks

首先，在 `Greenworks` 的根文件夹中运行 `Greenworks`，以便正确安装所有依赖项。

```
npm install
```

然后，你可以构建 `Greenworks` 本身。我们需要一个名为 `node-gyp` 的 `node.js` 构建工具。

首先，通过运行以下命令全局安装 `node-gyp`：

```
npm install node-gyp -g
```

然后，通过在 `Greenworks` 项目的根目录中运行以下命令来构建 `Greenworks`：

```
node-gyp rebuild --target=<electron_version> --arch=x64 --dist-url=https://atom.io/download/atom-shell
```

使用你安装的 `electron` 版本更改 `electron` 版本，例如 12.0.7 ，相关文件会在里面：build\Release\greenworks-win64.node

> 了解更多构建 `Greenworks` 的信息：https://github.com/greenheartgames/greenworks/blob/master/docs/build-instructions-electron.md

将 `Greenworks` 复制到你的 `Electron` 项目并加载它。

现在，你需要将一些文件复制到你的 `Electron` 项目中。

首先，在 `Electron` 项目的根文件夹中创建一个名为 `Greenworks` 的文件夹。在其中，复制 `Greenworks` 项目根文件中的 `greenworks.js` 文件和 `lib` 文件夹。

在 `lib` 文件夹中，将文件 `greenworks-win64.node` 替换为你在上一步中构建的文件。

现在只需在你的电子项目的 `index.js` 文件顶部添加以下行：

```
const greenworks = require(‘./greenworks/greenworks’);
```

大功告成！现在可以记录 `Greenworks` 对象以查看它具有哪些方法。

### 启动 Greenworks

要启动 `Greenworks`，需要做的第一件事是创建一个名为 `steam_appid.txt` 的文件，其中包含你的应用程序 `ID` 而没有其他任何内容。应用程序 `ID` 是商店中的那个，比如我的游戏《Wirewalk()↳》，Steam 是1636700。

> 不要将此 `txt` 文件复制到本教程最后一步的最终版本中，因为它仅用于测试目的。

现在，可以启动 `Greenworks` 了！

```
      let relaunch = greenworks.restartAppIfNecessary(1636700);
 
      if(relaunch){
        app.quit();
      }
      else{
        if(greenworks.init()){
          console.log('Steam inited successfully');
          loadWindow();
        }
        else{
          app.quit();
        }
      }
```

启动 `Greenworks` 应该是你收到“ready”事件后要做的第一件事。

`restartAppIfNecessary` 函数可防止游戏在 Steam 之外启动并通过从 Steam 打开它来重新启动它。但这只是为了发布——因为文件 `steam_appid.txt` 的存在会使 `restartAppIfNecessary`总是返回 `false`。

此外，在测试时，请确保 Steam 已打开并正在运行，并且你拥有游戏 ID。

# Cocos 和 Electron 之间的通信 - 成就

现在，让我们通过实现 Steam `成就功能` 来举例说明使用 `Steamworks` 的功能。

首先在 `Steamworks` 网站上创建一个成就：

请记住，`API` 名称是我们需要传递给 `greenworks` 的名称。

在 Cocos Creator 上，让我们创建一个`类`来处理与 `Electron` 的通信，你可以在成就逻辑确定发布成就的时间后调用该类。此代码在 `TypeScript` 中：

```
export default class Electron
{
    static releaseAchievement(id: string): void
    {
        (window as any).electron.ipcRenderer.invoke('releaseAchievement', id);
    }
}
```

现在在 Cocos 中，你将调用：

```
Electron.releaseAchivement('bad_cats');
```

该方法将在全局范围内寻找一个名为 `electron` 的对象。这个 `electron` 对象将成为我们与 Cocos 进程中的 `Electron` 进程进行通信的大门。我们将通过调用 `invoke` 方法进行通信，使用字符串参数来标识要调用的回调，后跟任意数量的参数。

现在，我们需要定义这个对象 `electron` 。我发现更简单的方法是通过编辑构建项目时由 Cocos 生成的结果`HTML`（在 `cocosExport` 文件夹中的 `index.html`），将以下内容添加到输出 `HTML`，在 `<body >` tag之前。

```
<script>
  window.electron = require('electron');
</script>
```

我们不能在游戏代码本身上设置这个 require('electron')， 因为 Cocos 会在构建时尝试找到 `electron js` 文件并给出错误，因为它无法找到它。

接下来，我们需要告诉 `Electron` 浏览器窗口的 `HTML` 可以与本机节点进程通信。本质上使 `electron.js` 可用。为此，我们需要在创建 `BrowserWindow` 时传递这些额外的参数。

```
webPreferences: { nodeIntegration: true, contextIsolation: false }
```

最后，在 `Electron` 上添加接收器功能。创建 `mainWindow` 后，可以在任何位置添加此代码。

```
  ipcMain.handle('releaseAchievement', (event, achievementString) => {
    console.log('releasing achievement', achievementString);
    greenworks.activateAchievement(achievementString);
  });
```

`handle`方法是与`invoke`匹配的方法。在这个例子中，`Electron` 收到 `releaseAchievement`息后，它会调用 `reenworks` `tivateAchievement` 告诉它为用户激活成就。

通过运行再次测试你的游戏：

```
electron .
```

# 将项目导出到 Windows、Mac 或 Linux

首先，通过运行全局安装 `electron-packager`。

```
npm install electron-packager -g
```

最后，运行以下行来构建你的游戏！

windows:

```
electron-packager . MyGame --platform=win32 --arch=x64 --overwrite
```

你也可以通过更改 `–platform` 值来构建 Mac 和 Linux，可以在 `electron` 文档中找到可用平台的列表。

> https://electron.github.io/electron-packager/master/modules/electronpackager.html#officialplatform

# [electron-packager打包速度太慢解决方案](https://segmentfault.com/a/1190000023791940)

# 方法一

在执行`electron-packager`前先运行`set ELECTRON_MIRROR=http://npm.taobao.org/mirrors/electron/`

# 方法二（推荐）

在`electron-packager`命令行加入参数
`--download.mirrorOptions.mirror=https://npm.taobao.org/mirrors/electron/`

**(Windows x64)完整版如下：**
`electron-packager . appName --platform=win32 --electron-version=10.0.0 --arch=x64 --download.mirrorOptions.mirror=https://npm.taobao.org/mirrors/electron/ --asar --app-version=0.0.0 --build-version=0.0.0 --out=outName --overwrite --no-package-manager --ignore="(.git)" --icon=logo.ic`