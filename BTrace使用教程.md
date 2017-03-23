
##Btrace使用教程

###下载

<pre>
	下载链接：https://github.com/btraceio/btrace/releases/tag/v1.3.9
</pre>

###安装及环境配置

<pre>
	1.下载一个压缩包
	2.解压
	3.配置环境变量 sudo vi /etc/profile 添加   export BTRACE_HOME=/home/josonliu/btrace     export PATH=$PATH:$BTRACE_HOME/bin PS:BTRACE_HOME必须是你解压的路径
	4.使配置生效  source /etc/profile  这样就可以在任何地方使用 btrace 命令了
</pre>

###BTrace简介及使用须知

<pre>
	BTrace是一个可以对 JAVA 进行安全、动态追踪的工具。为了保证在追踪动作的只读性，追踪动作不能改变程序的状态。一般来说 BTrace 具体有以下限制（禁令）：
	1.不准创建新对象！
	2.不准创建新数组！
	3.不准抛出异常！
	4.不准捕捉导常！
	5.不准使用断言或静态方法 只准使用 com.sun.btrace.BTraceUtils 中定义的类和方法及脚本里定义的 static 方法。
	6.不准对追踪的类或对象进行赋值操作
	7.不准使用外部、内部、嵌入或本地类
	8.不准实现接口
	9.不准使用循环
	其实就是一句话 只使用 println() 方法进行打印信息就好 哈哈
</pre>

###使用方法

<pre>
	1.找到要监控的 JVM进程 PID 
		a.通过 top -c 命令找到
		b.通过 ps -ef | grep 对应进程标识
	2.切换到进程拥有账户 一般为 www-data
		a.sudo -s 切换到 root 账户
		b.btracec 监控脚本  对监控脚本进行预编译 这一点很重要，可以在运行前发现错误。特别是应用到线上环境，必须强制先预编译一下，看是否报错。
		c.sudo -u www-data btrace $PID $监控脚本
	3.如需修改监控只需要停止运行后 修改脚本 然后运行脚本即可。
	4.BTrace脚本在进程重启后会失效。
</pre>

###使用场景

<pre>
	1.查看某一个方法中入参
	2.查看某一个方法的响应时间
	3.查看某一个方法中所有外部调用的响应时间，方便定位方法响应慢的具体位置及原因
	4.查看谁构造了一个超大的 ArrayList
	5.查看谁调用了 System.gc(),及其对应的调用栈
</pre>

###实战DEMO

<pre>
1.监控指定方法的耗时
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@BTrace
	public class CheckOnlineStatus{
		//监控某一个方法的执行时间
		@OnMethod(clazz = "com.joson.btrace.service.impl.BtraceServiceImpl",method = "getCount",location=@Location(Kind.RETURN))
		public static void printMethodRunTime(@ProbeClassName String probeClassName,@Duration long duration){
			println(probeClassName + ",duration:" + duration / 1000000 + " ms");
		}
	}
	这里是监控 BtraceServiceImpl 方法中 getCount 的调用情况。duration是以纳秒为单位的,所以换算成 MS 比较好看一点 ，其他例子也是如此考虑。

2.监控指定函数中所有外部调用的耗时情况.PS:这里最好只监控一个函数  太多的话 性能没法看
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@BTrace
	public class CheckOnlineStatus{
		//监控某一个方法的执行时间
		@OnMethod(clazz = "com.joson.btrace.service.impl.BtraceServiceImpl",method = "getCount",
		location=@Location(value=Kind.CALL,clazz="/.*/",method="/.*/",where = Where.AFTER))
		public static void printMethodRunTime(@Self Object self,@TargetInstance Object instance,@TargetMethodOrField String methon,@Duration long duration){
			if( duration > 5000000 ){//如果耗时大于 5 毫秒则打印出来 这个条件建议加 否则打印的调用函数太多 具体数值可以自己调控
				println(methon + ",cost:" + duration / 1000000 + " ms");
			}
			
		}
	}
	这里是监控 BtraceServiceImpl 类中 getCount 方法内的外部方法调用情况并打印出响应时间大于 5 MS 的外部调用方法名 。
	通过注入 @TargetInstance 和 @TargetMethodOrField 参数，告诉脚本实际匹配到的外部函数调用的类及方法名(或属性名) 
3.按接口、父类监控方法的执行
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@BTrace
	public class InterfaceMonitor{
		//监控某一个方法的执行时间
		@OnMethod(clazz = "+com.joson.btrace.service.BtraceService",method = "getCount",location=@Location(Kind.RETURN))
		public static void printMethodRunTime(@ProbeClassName String probeClassName,@Duration long duration){
			println(probeClassName + ",cost time:" + duration / 1000000 + " ms");
		}
	}
	这里是监控 BtraceService 接口的所有实现类中 对 getCount 方法的调用情况。
4.正则表达式定位监控
	通过正则表达式可以实现批量定位，正则表达式需要写在两个 "/" 中间。PS:建议正则表达式的范围要尽可能的小，不然会非常慢。
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@BTrace
	public class ServiceMonitor{
		//监控某一个方法的执行时间
		@OnMethod(clazz = "/com.joson.btrace.service.*/",method = "/.*/",location=@Location(Kind.RETURN))
		public static void printMethodRunTime(@ProbeClassName String probeClassName,@ProbeMethodName String probeMethod,@Duration long duration){
			println( probeClassName + "." + probeMethod + " cost time: " + duration / 1000000 + " ms.");
		}
	}

	这里是监控 com.joson.btrace.service 包下的所有类与方法，并打印其调用时间 以 MS 为单位。
	通过在函数里注入 @ProbeClassName,@ProbeMethodName 参数，告诉脚本实际匹配到的类和方法名。
5.监控代码是否到达了某类的某一行 
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@OnMethod(clazz = "java.net.ServerSocket", location = @Location(value = Kind.LINE, line = 363))
	public static void onBind4() {
	   println("socket bind reach line:363");

	}
	这里是监控代码是否到达了 Stock类的 363 行。
6.打印某个类中 某一方法的入参
	import com.sun.btrace.AnyType;
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@BTrace
	public class ServiceMonitor{
		//监控某一个方法的执行时间
		@OnMethod(clazz = "com.joson.btrace.service.BtraceService",method = "getCount",location=@Location(Kind.RETURN))
		public static void printMethodRunTime(@Self Object self,String type,Integer limit,@Return AnyType result ){
			println( "type: " + type + " ,limit: " + limit  );
			println("result : " + result );
		}
	}
	这里是监控 BtraceService 类中 getCount 方法的所有入参及返回值
	对于入参，不需要打印的也可以不定义 但是定义一定要按顺序。比如参数列表不能放在返回值的后面。
	对于返回值类型 如果是非基本类型 则直接用 AnyType 类型即可。
7.查看谁调用了 GC
	import com.sun.btrace.annotations.*;
	import static com.sun.btrace.BTraceUtils.*;
	@OnMethod(clazz = "java.lang.System", method = "gc")
	public static void onSystemGC() {
	    println("entered System.gc()");
	    jstack();// print the stack info.
	}
8.其他用法参考链接：https://github.com/btraceio/btrace/tree/master/samples
</pre>
