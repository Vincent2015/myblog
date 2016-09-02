1，process对象可以查看当前环境NW的版本及对应chromium的版本	

    process.versions['nw']// process.versions['chromium']

2.node模块引用上下文,require(../?)

-  If the parent file is running in Node context, the child’s relative path is treated as relative to its parent.
- If the parent file is running in browser context, the child’s relative path is treated as relative to the application’s root directory, i.e. the directory of manifest file.

3,flash plugin


4,win.print