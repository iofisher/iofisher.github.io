---
layout: post
title: 如何在GitHub上构建博客
category: Jekyll
date: 2014-10-21
---
本文所述是基于Windows操作系统。

### **1. 下载Ruby软件包**  ###
Ruby 2.1.3（根据需要选择32位或64位）  
DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe（与Ruby版本对应一致）  
[下载地址](http://rubyinstaller.org/downloads/)

![ruby下载页面](http://iofisher.qiniudn.com/%40%2Fblog%2Fruby_installers.jpg)

### **2. 安装Ruby2.1.3** ###
![](http://iofisher.qiniudn.com/%40%2Fblog%2Finstall_ruby.jpg)  
选项全部勾选上。注意，路径中一定不要有空格！  
本人第一次安装的时候采用的是“D:\Program Files\Ruby21”这样的路径，在后面安装jekyll时遇到很多问题，后来执行了下RubyDevKit中的msys.bat时提示“D:\Program”找不到，由此可判断是因为路径的问题。

### **3. 安装Ruby Dev Kit** ###
双击下载得到的DevKit-mingw64-32-4.7.2-20130224-1151-sfx.exe，解压缩到“D:\RubyDevKit”。  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Finstall_rubydevkit.jpg)


### **4. 执行命令** ###
cmd进入“D:\RubyDevKit”。  
执行命令：`ruby dk.rb init`和`ruby dk.rb install`，至此Ruby环境配置完毕。  
接下来安装jekyll，执行命令：`gem install jekyll` （可直接在“D:\RubyDevKit”的msys.bat中输入）。  
默认源地址是https://rubygems.org/，由于网络原因很难安装上，最好切换到国内镜像，使用过程为：


- 移除默认源：`gem sources --remove https://rubygems.org/`
- 添加taobao源：`gem sources -a https://ruby.taobao.org/`  

- 查看当前源（确保只有ruby.taobao.org）：`gem sources -l`  
     *** CURRENT SOURCES ***

     https://ruby.taobao.org
  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Finstall_ruby_cmd.jpg)  
另外，尽管切换到taobao源，可能仍然还有网络问题，本人遇到下面这个问题，再执行下`gem install jekyll`就好了。  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Finstall_ruby_network_problem.jpg)  
在cmd中用命令行`jekyll`测试是否安装成功。

### **5. 代码语法高亮** ###
方式一：安装基于Ruby的Rough。  
执行命令：`gem install rouge`；
然后在_config.yml中添加设置项：`highlighter: rouge`。但比第二种方式支持的语言要少。  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Fgem_install_rough.jpg)  

方式二：安装基于Python的Pygments。  
A、首先要安装Python，[下载地址](https://www.python.org/download/)。  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Finstall_python.jpg)    
B、安装pip（它是用来安装或管理Python包的工具，就像Ruby的Gem一样）。下载get-pip.py，[地址](https://pip.pypa.io/en/latest/installing.html)。将get-pip.py放到一个文件夹内，cmd进入该文件夹，执行命令`python get-pip.py`。  
C、安装Pygments。执行命令`python -m pip install Pygments`。  
D、最后在_config.yml中添加设置项：`highlighter: pygments`。

另外，  
（1）在_config.yml中最好指定编码格式：encoding: utf-8，用于发表博客的md文件一定要保存成不带Bom的UTF-8格式（可以在Editplus中转换），否则可能没法解析。  
（2）要想CMD中内容按照UTF-8编码，那么每次打开CMD时运行命令`chcp 65001`即可。

至此，Jekyll安装完毕！

### **6. 本地搭建测试博客** ###

在某个文件夹路径下使用命令：  
`jekyll new myblog` （myblog为项目名称）  
`cd myblog`  
`jekyll serve`  
项目内容包含如下：  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Fjekyll_demo_blog.png)  
其中“\_config.yml”为配置文件，“\_posts”是博客文章，“\_site”是页面，“css”用于定义博客样式。  
在执行`jekyll serve`这条命令时，可能出现“Liquid Exception: cannot load such file -- yajl/2.1/yajl in _posts/xxx.markdown”这样的错误，那么一种解决方法是将_config.yml文件代码高亮属性改为`highlighter: rouge`;另一种是在保持`highlighter: pygments`不变的情况下，执行命令`gem install yajl-ruby --platform=ruby`（[https://github.com/brianmario/yajl-ruby/issues/116](https://github.com/brianmario/yajl-ruby/issues/116)）。  
然后在浏览器中输入http://localhost:4000/即出现默认的简单blog，如下：  
![](http://iofisher.qiniudn.com/%40%2Fblog%2Fsimple_demo_blog.png)  
根据个人喜好，可以搭建自己喜好的模板风格。[https://github.com/jekyll/jekyll/wiki/sites](https://github.com/jekyll/jekyll/wiki/sites)列出了许多博客站点及其模板源码，我们可以获取源码，然后按照要求配置，即可在本地文件夹下构建博客（执行`jekyll serve`）。

### **7. 申请github账号，创建博客仓库** ###
为了将博客搬到github上，我们需要申请一个github账号，具体申请过程省略。注意直接托管在github上的博客，需要创建名为xxx.github.io的仓库，其中xxx为github的账号名，http://xxx.github.io即为你的博客域名。最后将本地测试通过的博客上传到github上，那么就可以根据域名访问了。
  


#### 参考资料： ####
《Run Jekyll on Windows》 [http://jekyll-windows.juthilo.com/](http://jekyll-windows.juthilo.com/)  
《Jekyll中文网站》：[http://jekyllcn.com/](http://jekyllcn.com/)