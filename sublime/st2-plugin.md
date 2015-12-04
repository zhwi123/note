# sublime text 2 插件

###Package Control  
sublime text使用包管理器来安装插件，所以要先安装`Package Control`。安装方法，使用`ctrl + ~`快捷键，调出console，在控制台中粘入如下代码即可完成安装(需重启)：  
```python
import urllib2,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```
如果无法通过上述代码安装，可以手动安装(测试发现可能会有部分依赖缺失)。  
1. 点击Preferences > Browse Packages菜单  
2. 进入打开的目录的上层目录，然后再进入Installed Packages/目录  
3. 下载[Package Control.sublime-package](https://packagecontrol.io/Package%20Control.sublime-package)  
4. 重启Sublime Text  
参考资料：  
* <https://packagecontrol.io/installation>  
* <http://www.imjeff.cn/blog/62/>  
* <https://github.com/wbond/package_control>

###插件安装  
打开Preferences > Package Control，调出包管理器，找到Install Package，即可在线搜索安装插件。

###Git插件安装配置  
在Package Control中搜索`Git`，即可找到Git插件,点击安装即可。
打开Preferences > Package Settings > Git > Settings Default，修改其中"git_command"配置的值为本机安装的Git执行文件，如我的机器上："D:/Program/Git/bin/git.exe"。  
使用Git插件，用`Ctrl + Shift + P`快捷键，打开命令窗口，输入`Git: init`来初始化Git本地仓库，ST2会让你选择要初始化的Git目录，任意指定目录即可。初始化完成后，即可正常在ST2中使用Git命令了。如，执行：`Git: status`来查看本地项目更改状态。可用命令有：  
```
Git: diff  
Git: add  
Git: commit 用来提交更改。ST2会自动跳出一个文本文件，你可以在文件的最上方输入comments，然后直接关闭(Ctrl + W)这个文件，就会出发commit操作。  
```

###Emmet插件安装配置  
搜索`Emmet`在线安装即可。代码生成快捷键是`Tab`或`Ctrl + E`，如：输入`html:5`，按`Tab`键，将生成Html5的文档。

###Markdown及[MarkdownPreview](https://github.com/revolunet/sublimetext-markdown-preview)插件安装  
搜索`MarkdownEditing`、`MarkdownPreview`在线安装即可。  
`MarkdownPreview`用法：`Ctrl + Shift + P`搜索`Markdown Preview: Preview in Browser`，即可打开浏览器，预览当前文件。  

###GBK字体支持插件  
ST2有时打开文件会显示乱码，可能是因为文件使用GBK编码，默认ST2不运行此编码，可以安装`GBK Encoding Suppor`和`ConvertToUTF8`插件来解决此问题。