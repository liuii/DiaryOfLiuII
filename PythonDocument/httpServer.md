# 快速创建一个简易的WEB服务器  
在实验上机的时候，经常需要快速建立一个机房内的简易`WEB服务器`，总结一下使用`Python3`来快速创建web服务器的方法：  


- 安装`python3`，下载地址：[`download python3`](https://www.python.org/downloads/)  
- 安装`Sublime3`，目的是用于编写`Markdown`文档，下载地址：[`download sublime3`](http://www.sublimetext.com)  
- 为`Sublime3`安装`Package Control`，安装脚本需要首先打开`sublime`的控制台，快捷键为<kbd>ctrl</kbd>+<kbd>`</kbd>，在控制台中输入如下代码：  
``` Python
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```
- 用`Package Control`安装`OmniMarkupPreviewer`，目的是用于为`Markdown`生成`index.htm`文件。  
- 准备好后，在控制台的当前目录下运行：`python -m http.server 80`  