UBANTU安装常用软件
---------------------

###安装JAVA
<pre>
1.添加PPA中的 JAVA 镜像
	sudo add-apt-repository ppa:webupd8team/java
2.更新软件库
	sudo apt-get update
3.安装JAVA
	安装JAVA7：sudo apt-get install oracle-java7-installer
	安装JAVA8: sudo apt-get install oracle-java8-installer
</pre>

###安装Sublime text 3
<pre>
1.添加PPA中 sublime text3仓库
	sudo add-apt-repository ppa:webupd8team/sublime-text-3
2.更新软件库
	sudo apt-get update
3.安装 sublime text 3
	sudo apt-get install sublime-text-installer
</pre>

###安装fcitx输入法
<pre>
1.添加PPA中 fcitx输入法仓库
	sudo add-apt-repository ppa:fcitx-team/nightly
2.更新软件库
	sudo apt-get update
3.安装输入法
	Google拼音：sudo apt-get install fcitx-googlepinyin 
	五笔：sudo apt-get install fcitx-table-wubi
	五笔拼音混合：sudo apt-get install fcitx-table-wbpy
</pre>

###安装 redis
<pre>
1.去 redis 官网下载最新稳定版 ： https://redis.io/download
2.解压 tar xzf redis-3.2.6.tar.gz  我这里下的是3.2.6 稳定版
3.编译 切换到 redis-3.2.6 目录  执行 make 命令进行编绎
4.进入到 utils 目录下 ，执行 install_server.sh 脚本，在安装时会提示输入端口号，其他的都默认即可。
</pre>
###安装 Intellij 
<pre>
1.去官网下载压缩包
2.解压
3.在BIN目录下运行 .sh 命令
4.激活网址 ： http://idea.iteblog.com/ 打开激活界面 在这个激活网址上生成激活码 然后粘贴即可。
</pre>
