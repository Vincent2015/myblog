   NW（node-webkit） 是一个用 HTML5/CSS/Javascript 这些 Web 技术来写跨平台应用程序的开源框架，可以让我们写一份代码，同时跑在 Windows、Linux 和 Mac 上，是国人主导的在国际上也很有影响力的开源软件。

   前端同学看到这个概念，自然而然的就先去试试，而且大多就是把原来写的web应用，直接用NW打包，就生成了一个应用。而且有一些公司也就是这么用的。因为今年上半年刚做了一个基于NW的客户端应用。这里借用《NW.js Essentials》这本书的一个概念叫做 NATIVE UI来说下（做过移动端的同学对于这个词可能比较熟悉）。

NW的Native UI提供了哪些东西呢？

![](/assets/BC0D7019-CFB6-4C30-86C3-77970A9BF8A4.png)

上面表格就是Native UI对应的API，对应的Native UI有App，window，Screen,menu,dialogs,tray,clipboard,shell.熟悉桌面系统的人都知道这些基本上是桌面系统最常见到的界面元素。如果对这些概念不熟悉，可以去NW官网去深入学习下。

要成为一个好的NW的应用，就需要做好web和Native的融合。需要让Native可以去接管web，完成native相关的处理。默认我们的web网页在浏览器打开就会有一个窗口，而且这个窗口默认是显示出来的。其实从这里，我们就可以让native介入，控制一些窗口行为。

var myApp = {};

myApp.mainWindow = myApp.gui.Window.get();
 
myApp.name = myApp.gui.App.manifest.name;

通过mainWindow可以控制NW应用窗口是否显示在桌面上,以便于处理应用启动过程中的一些处理，处理完后才显示可见窗口。

myApp.mainWindow.show();

可以为mainWindow增加事件

 myApp.mainWindow.on('close', function () {}）

 myApp.mainWindow.on('open', function () {}）

 创建的menubar，menu，context menu都是添加到窗口上的，所以接管窗口非常重要。

除了接管窗口myApp.gui.Window.open('option.html')还可以单独再开一个nw的window.

window是NW里的重要部件，因此先关的API，event也最丰富。

部分API

**Window.title**: This sets or gets the window title at runtime

**Window.cookies.***: This lets you set or get window cookies

**Window.menu**: This associates a menu to the window (we're going to deal with it later in this chapter)

**Window.reload()**: This reloads the window

**Window.reloadIgnoringCache()**: This reloads the window, thus cleaning the cache

**Window.setAlwaysOnTop()**: This sets the window at the top of all the other applications' windows

**Window.isTransparent and Window.setTransparent(transparent)**: These APIs allows you to set the background of the window as transparent (for example, Adobe Photoshop splash screen)

**Window.showDevTools([id | iframe, headless])**,Window. closeDevTools() and Window.isDevToolsOpen(): These let you open, close, or check for the visibility of DevTools at runtime

**Window.capturePage(callback [, image_format | config_object ]):** This takes a screenshot of the window

Window.eval(frame, script): This evaluates a given script in the provided frame

Window.zoomLevel: This sets or gets the window zoom level (it might be useful when dealing with 4k displays)

部分Event

**capturepagedone**: This is fired when Window.capturePage() succeeds; a buffer argument is passed

**devtools-opened and devtools-closed**: This is emitted when DevTools is opened or closed

**zoom **: This is fired when the window is zoomed; a parameter with the zoom level is passed

**loading and loaded**: This is relatively emitted when the window starts to reload and when the window is fully loaded (an alternative to window.load that doesn't rely on the DOM)

**document-start**: This is fired when the document object is available, but before any other DOM object is constructed or any script is run, a frame attribute will be passed if we are dealing with an iframe

**document-end** This is fired when the document object is fully loaded; before the onload event is emitted, a frame attribute will be passed if we are dealing with an iframe




