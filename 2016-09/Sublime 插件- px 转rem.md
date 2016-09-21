Sublime 插件- px 转rem

一个CSS的px值转rem值的Sublime Text 3自动完成插件。

插件效果如下：



安装

克隆项目 https://github.com/hyb628/cssrem.git

进入packages目录：Sublime Text -> Preferences -> Browse Packages...

复制下载的cssrem目录到刚才的packges目录里。

重启Sublime Text。

配置参数

参数配置文件：Sublime Text -> Preferences -> Package Settings -> cssrem-->settings user

px_to_rem - px转rem的单位比例，默认为40。

max_rem_fraction_length - px转rem的小数部分的最大长度。默认为6。

available_file_types - 启用此插件的文件类型。默认为：[".css", ".less", ".sass"]。

我现在这边配置好的文件，可参考：

{ "px_to_rem":36, "max_rem_fraction_length":3, "available_file_types":[".html",".css",".less",".sass"],}
