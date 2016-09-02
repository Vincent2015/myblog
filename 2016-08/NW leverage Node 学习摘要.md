1，Routing and templating in NW.js
you can also write a very simple view loader
yourself as, in a desktop application, you won't need to change URLs:

	<div id="mainView"></div>
	<script>
	var myApp = {};
	myApp.loadView = function (view, containerId, callbackName) {
	var fileName = 'views/' + view + '.html';
	$(containerId).load( fileName , function (response, status) {
	if (status === 'error'){
	console.warn('Could not load ' + fileName);
	} else if (typeof myApp[callbackName] === 'function') {
	myApp[callbackName]($(this));
	}
	});
	};
	// Loading views/view1.html
	myApp.loadView('view1', '#mainView', 'view1Controller');
	myApp.view1Controller = function ($currentView) {
	// Do something after main view is loaded
	};
	</script>

If you don't want to rely on a web
application framework but rather keep using a Node.js template engine, you can easily
do it directly from the DOM. Let's review the loadView() function of the previous
example in order to load Swig templates:

	myApp.loadSwigView = function (view, data) {
	var fileName = 'views/' + view + '.html',
	swig = require('swig');
	return swig.renderFile(fileName, data||{});
	};
	// Loading views/swigTemplate.html
	var template = myApp.loadSwigView('swigTemplate', {
	pagename: 'awesome people',
	authors: ['Paul', 'Jim', 'Jane']
	});
	$('#mainView').html(template)

2，Node.js global and process objects

In NW.js, the global object works much like it does in Node.js with the peculiarity
of being accessible from within the DOM, giving an additional way to share objects
between the window and Node.js contexts.
 Moreover, when the window object is created,
all the members of the global object are assigned to it, which is why you can use
require() from within the DOM.


The process object is a global object that can be accessed from both the window and
Node.js contexts. In NW.js, some new fields have been added to it:

- • process.versions['NW.js']: This gets the NW.js version
- • process.versions['chromium']: This gets the NW.js chromium version
- • process.mainModule: This gets the start page (such as index.html)
   specified in the manifest file's main field
If node-main has been specified in the manifest file, process.mainModule points to
the specified file.

3,The window object

When all the initial stuff has been done, as soon as it's ready, the window object is
passed to Node.js inside the global object so that you can reference it in a module as global.window or simply window. The window reference is then replaced with a new
one on page navigation.

4.Using NW.js' main module
The node-main field acts similarly, but instead of referring to an HTML file, it will
allow you to set a main Node.js module, which will run in the background through the
application's life cycle independently of the window context (even though it will quit by default when all the application windows are closed).

Thanks to the global object, the window object is also shared
with the main module, but you can't obviously access it until a
ready signal is sent from the DOM.

5,Handling paths in NW.js
Application root
- data/myData.json
- js/script.js
- modules/myModule.js
- modules/otherModule.js
- index.html
- package.json

(1)realtive path

	require('./modules/myModule');

When calling a module from another module, however, you should use the relative
path from the current file.

	require('./otherModule');
	
	var fs = require('fs');
	fs.readFile('data/myData.json', 'utf8', function (err, data) {
	console.log(JSON.parse(data));
});

(2) absolute path

There are two ways to get the absolute path of the application root folder. You
might, of course, call window.location.pathname on index.html, but there's a
much better Node.js way to do it. Node.js provides a __dirname global object,

However, in order to use __dirname inside the window context,
we can provide a simple workaround. We can create a util.js module inside the
application root folder:

	exports.dirname = __dirname;

(3)normalize

	var path = require('path');
	console.log(path.normalize('/my/custom/folder'));
On Mac OS X or Linux, path.**normalize**() will return /my/custom/folder, but on
Microsoft Windows, it will return \\my\\custom\\folder


6.NW.js context issues

One more thing that might be very confusing at first is that the same objects from
different contexts also have different constructors in their prototype chain. That
results in misbehavior of the instanceof operator, which will not be able to recognize
prototypes coming from a different context.

Let's check an example:

	• nodeContext.js:
	exports.myArray = ['one','two','three'];
	• index.html:
	var windowContext = {
	myArray: ['one','two','three']
	};
	// First statement
	console.log(windowContext.myArray instanceof Array); //
	true
	var nodeContext = require('./nodeContext');
// Second statement
console.log(nodeContext.myArray instanceof Array); // false
As illustrated, we're getting different results despite it looking like we're checking for
the very same array object. A faster way to avoid this kind of problem is to get rid of
instanceof in favor of a more reliable alternative, such as Array.isArray. If you
can't afford it, you can take advantage of the nwglobal module (https://github.
com/Mithgol/nwglobal).
A very simple way to export Node.js constructors is provided by nwglobal in order
to avoid instanceof issues in both the window and Node.js contexts. Let's review
the second statement of the previous example:
var nwglobal = require('nwglobal');
console.log(nodeContext.myArray instanceof nwglobal.Array); //true
This time, the statement will return true as we get the right constructor with
nwglobal.Array.

7.Working with Node.js modules
One of the strengths of Node.js is surely the availability of tens of thousands of
modules that are freely downloadable with npm. As you probably know already,
Node.js supports three kinds of modules:
• Internal modules, which are parts of Node API
• Third-party modules written in JavaScript
• Third-party modules with C/C++ add-ons

1. Install nw-gip with the following command:
npm install nw-gyp –g

	On Mac OS X, the only requirement is to install Xcode command-line
	tools; on other operating systems, you'll have to manually install Python,
	Make, and a proper C/C++ compiler. You can find more information
	about this at https://github.com/nwjs/nw-gyp.

2. Install your module as usual with npm.
3. Run nw-gip in the module folder (where the binding.gyp file is located),
providing the NW.js target version:
$ nw-gyp rebuild --target=0.11.2
4. That's it. Now you can require your module as usual:
   var modulename = require('modulename');