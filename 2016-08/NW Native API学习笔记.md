0,There's one more thing to consider when using Native UI APIs; if you're doing
something wrong, the app will crash—no exceptions thrown! That's why, you should
follow these good practices:

	• Remember to assign a null value to deleted/removed elements in order to
	avoid misbehaviors in case you reuse them
	• When possible, do not recreate UI elements—reuse them
	• Do not reassign elements
	• Do not change the UI type's prototype

1，When a NW.js application starts, it may take a bit between the time when
the main window is displayed and the time when the code is evaluated.
In order to give a better user experience, you'd rather hide the main window
until all the background jobs have been completed. To achieve this, you'll have
to hide the application editing the manifest file as follows:

    {
     "window": {
      "show": false
      }
    }
    Then, you can exploit the onload event:
    var gui = require('nw.gui');
    window.onload = function() {
    gui.Window.get().show();
    }

That will eventually show the window when everything is fully loaded.
Another use for this technique is for creating background applications
that live in the system tray.

2.As stated in the introduction, window is nothing more than a
wrapper of the DOM window object.

    var gui = require('nw.gui');
    var currentWindow = gui.Window.get();
    var language = currentWindow.window.navigator.language;
    console.log(language);

3,Instantiating a new window object
There are many ways to instantiate a window object. When you need to refer to the
current window, all you have to do is to call the Window.get([window_object])
method leaving the attribute empty, as follows:

    var gui = require('nw.gui');
    var currentWindow = gui.Window.get();
In order to instantiate a different window object, you can proceed with passing the
DOM window object to it through the get method:

    var gui = require('nw.gui');
    var extraWindow = gui.Window.get(
    window.open('window.html');
    );
Otherwise, use the Window.open(url[, options]) method:

    var gui = require('nw.gui');
    var extraWindow = gui.Window.open('window.html', {
    position: 'center',
    width: 640,
    height: 480,
    focus: true
    });

As illustrated, the open method has an optional attribute, which allows you to set all the options of the window. You can find a reference of all the available settings in Chapter 6, Packaging Your Application for Distribution, as they are the same used in the manifest file .

There are also some custom options that can be used with the open method:


- new-instance: true: This will open the window in a new WebKit process.


- inject-js-start: path/to/script.js or inject-js-end: path/to/
script.js: This will run the provided JavaScript code before any other
DOM is constructed or after the document object is loaded but before the
onload event is fired.


4,Closing windows
Let's say, for example, we have an application with a main window and a settings
window and we want to achieve the following result:
• Closing the main windows should close all the application windows and quit
• Closing the settings window should save the settings
Hence, when closing the main windows, the settings window should have an
opportunity to save the data before quitting. Here's how we can implement that:

    • index.html (main window):
    var gui = require('nw.gui'),
    mainWindow = gui.Window.get();
    window.open(settings.html');
    mainWindow.on('close', function() {
    gui.App.closeAllWindows();
    this.close(true);
    });
    • settings.html (settings window):
    var gui = require('nw.gui'),
    settingsWindow = gui.Window.get();
    settingsWindow.on('close', function() {
    // Save data here
    this.close(true);
    });

A special note should be added to Window.close([force]) as this method and the
relative event, which we saw previously in the App.closeAllWindows() example,
can be really handy to save data when closing a window. Moreover, all the shutdown
work might slowdown the closing process, so, in the while, we'd rather hide the
window in order to improve user experience. Here's a simple example:

    var gui = require('nw.gui'),
    currentWindow = gui.Window.get();
    currentWindow.on('close', function (event) {
    // Hide the window
    this.hide();
    // Save data
    // ...
    // Close the window
    this.close(true);
    });
    currentWindow.close();

The closing process provides another event, which can be used when more than one
window are at play. I'm talking about the closed event, which is triggered when a
window is closed and all the associated JavaScript objects are released:

	var gui = require('nw.gui'),
	newWindow = gui.Window.open('window.html');
	newWindow.on('closed', function () {
	newWindow = null;
	});

In the preceding example, you can see we have respected the good practice of assigning
null to an object when the object is being removed. Later in the code, we might
check for the extraWindow variable value to be different from null in order to verify
that it's still available.

5,App.on('reopen', callback): This works only on Mac OS X; it's fired
when the user clicks the dock icon for an already running application.


Kiosk mode currentWindow.enterKioskMode()/currentWindow.leaveKioskMode()



6.The taskbar icon – get the user's attention!

- **Window.setShowInTaskbar(bool show)**: This allows the setting of the show
  attribute to false, which will hide the icon.
- **Window.setBadgeLabel(string label):** This is a pretty cool and mostly
unknown function of NW.js. It allows the showing of a string of text inside
a little badge near the application icon. It works fine in Mac OS X and Microsoft Windows, but does not work on most Linux distributions, except for Ubuntu, which can display only numeric values.
- **Window.requestAttention(number|bool count):** Sometimes, you just
need to get the user's attention when your application is minified or not
focused. Window.requestAttention() behaves differently depending on
the operating system. On Mac OS X, the application must not be focused
in order to fire requestAttention; if the count parameter is set to -1, the icon
will blink once, while if set to 1, it will blink until you give it some attention.
On Microsoft Windows, both the icon and the window will blink the
number of times set in the count parameter, for example, currentWindow.
requestAttention(5). On Linux (tested on Ubuntu + Cinnamon DE), all
values of count will work except for 0; the icon will not blink if the window
is already active.
- **Window.setProgressBar(number progress):** When running timeconsuming
processing, it might come in handy to show a simple progress
bar over the application icon in order to keep the user updated while they
are doing something else. The progress attribute takes values in the range
between 0 and 1, so, for example, 0.5 will be half way.

7,Other Window APIs
There are a few other Window-related APIs:

- Window.title: This sets or gets the window title at runtime
- Window.cookies.*: This lets you set or get window cookies
-  Window.menu: This associates a menu to the window (we're going to deal
 with it later in this chapter)
- **Window.reload()**: This reloads the window
- Window.reloadIgnoringCache(): This reloads the window, thus cleaning
the cache
-  Window.setAlwaysOnTop(): This sets the window at the top of all the other
 applications' windows
- Window.isTransparent and Window.setTransparent(transparent):
These APIs allows you to set the background of the window as transparent
(for example, Adobe Photoshop splash screen)
-  Window.showDevTools([id | iframe, headless]), Window.
- closeDevTools() and Window.isDevToolsOpen(): These let you open,
close, or check for the visibility of DevTools at runtime
-  **Window.capturePage(callback [, image_format | config_object ]):**
   This takes a screenshot of the window
- Window.eval(frame, script): This evaluates a given script in the
 provided frame
- Window.zoomLevel: This sets or gets the window zoom level (it might
 be useful when dealing with 4k displays)


There are also many events we can listen for in order to implement
better applications:

- • capturepagedone: This is fired when Window.capturePage() succeeds; a
 buffer argument is passed
- • devtools-opened and devtools-closed: This is emitted when DevTools is
 opened or closed
- • zoom : This is fired when the window is zoomed; a parameter with the zoom
 level is passed
- • loading and loaded: This is relatively emitted when the window starts to
 reload and when the window is fully loaded (an alternative to window.load
 that doesn't rely on the DOM)
- • document-start: This is fired when the document object is available, but
 before any other DOM object is constructed or any script is run, a frame
 attribute will be passed if we are dealing with an iframe
- • document-end This is fired when the document object is fully loaded; before
the onload event is emitted, a frame attribute will be passed if we are
dealing with an iframe


8,The Shell API – platform-dependent
desktop functions
The Shell API is made of a set of methods that allow you to leverage platformrelated
tasks:

    • Shell.openExternal(String URI) : This will open any given URI
    (http://, mailto:, and so on) with the default system application if one has
    been associated; otherwise, nothing will happen.
    • Shell.openItem(String file_path): This will open any given file with
    the default system application if one has been associated; otherwise, the
    Open with dialog will be shown.
    • Shell.showItemInFolder(String path): This will open the given path
    inside the system file explorer/finder.
    Here's a simple example to open Google.com on your default browser:
    var gui = require('nw.gui');
    gui.Shell.openExternal('http://www.google.com');

9,notes

- new menu item:

		var menuItem = new gui.MenuItem({
		type: 'checkbox', // Can be normal, checkbox or separator
		label: 'Menu Checkbox',
		icon: 'icon16.png',
		tooltip: 'Hello World!',
		click: function () {
		// Do something on click
		},
		enabled: true,
		checked: false, // Only for checkboxes
		key: 'M',
		modifiers: 'ctrl-shift',
		iconIsTemplate: true, // Only on Mac OS
		submenu: new gui.Menu() // Accept a Menu Object
});
- File dialogs – opening and saving files

- The NW.js Clipboard API is still pretty young. At the moment, you
can only access plain text and only from the system clipboard. The
 selection clipboard in X11 is not supported.

- As it's beyond the purpose of the book to dive deep into Mac OS X custom features, I will list them here for reference:

		• Tray.title: This lets you set or get custom text to show beside or instead of
		the icon.
		• Tray.alticon: This lets you get or set an active icon shown when the menu
		attached to the tray icon gets opened.
		• Tray.iconsAreTemplates: On Mac OS X, icons can be treated as templates
		(true by default

different dataPath could have diiferent nw instance