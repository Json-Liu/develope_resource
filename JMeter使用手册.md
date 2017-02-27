#JMeter 使用手册
##下载安装
<pre>
	1.JMeter最新版下载链接：
		http://jmeter.apache.org/download_jmeter.cgi
	2.解压下载好的压缩包 配置环境变量（如果不配则需要到 JMeter的bin目录下通过 jmeter.bat 才能启动 jmeter ）
</pre>
##使用步骤
<pre>
	1.通过图形界面配置好相应的 JMeter 测试计划 在WINDOWS 配置即可
	2.通过 NON-GUI 即命令模式运行 JMeter 测试计划
	3.生成测试报表进行分析
</pre>
##图形界面配置 JMeter 测试计划
这里以创建 WEB 测试计划为例
<pre>
	1.创建线程组
	2.创建 HTTP 请求
	参考链接：http://jmeter.apache.org/usermanual/build-web-test-plan.html
</pre>
注意事项
<pre>
	1.线程组的用户数 即并发数。这个要根据实际项目的情况进行配置。比如你的项目需要支持200并发，即200TPS 那你就配200 。需要支持2000TPS就配置2000.
	2.Ramp-Up Preriod 表示这个用户在多少秒类请求完。配置为 0 时表示一次全部请求完，配置为 1时表示 1 秒内请求完。
	3.HTTP请求中的动态配置。比如用户UID 这个参数你需要动态，建议将UID列表放到一个CSV文件中，然后添加一个 CSV DataSet 并将其变量名命名为 UIDS 这样就可以通过 ${UIDS} 来表示从CSV文件中读取UID 进行轮询请求达到动态的效果。
</pre>
##通过 NON-GUI 运行 JMeter 测试计划
<pre>
	JMeter Non-GUI 命令解析：
		-n : 声明 jmeter 是否在 non-gui 模式下运行。添加此参数表明是在 non-gui 模式下运行，否则不是
		-t : 包含测试计划的 JMX 文件
		-l ：纪录测试计划结果的 JTL 日志文档
		-j : 需要运行的 日志文档
		-r : 声明测试计划将运行在远程配置的服务器上
		-R : 远程服务器列表
		-g : 用来保存生成的报表相关文件的文件路径
		-e : 在加载测试计划后生成报表
		-o : 根据 JTL 日志文档生成 对应的分析报表
		-H : 代理服务器IP或域名
		-P : 代理服务端口
	命令格式： jmeter -n -t your_jmeter_test_plan.jmx -l your_jmeter_result.jtl 
	命令解析：在 non-gui 模式下 运行 your_jmeter_test_plan.jmx 测试计划，同时将测试计划的结果保存到  your_jmeter_result.jtl 文件中
</pre>
## 生成测试报表进行分析
<pre>
	命令格式： jmeter -g your_jmeter_result.jtl -o report
	命令解析： 将 your_jmeter_result.jtl 生成相关的分析报表到 report文件夹中，PS: report文件夹在运行命令前不能存在。
</pre>
