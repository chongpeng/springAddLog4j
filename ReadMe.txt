
项目采用spring 框架时，只需在web.xml 中加入 一段配置文件。

使用：
Logger logger = Logger.getLogger(ReportController.class.getName());
logger.info(user.getName()+"   "+"项目期："+subproject.getName());



如果不在框架中使用 （程序中添加配置文件：配置文件放在src目录下）：  PropertyConfigurator.configure("log4j.properties");
详细解读：


Log4j 是Apache的一个开放源代码项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件、甚至是套接口服务器、NT的事 件记录器、UNIX Syslog守护进程等；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。最令人感兴趣的就 是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

     如此强大的优越性，实际上手并不难，尤其在spring框架下，使用log4j更是容易，下面介绍一下spring下的log4j应用。

    当然先要下载相应的jar包（log4j.jar）

首先是web.xml的配置，在web.xml中加入如下配置

   <context-param>     
      <param-name>     log4jConfigLocation</param-name>     
      <param-value>/WEB-INF/props/log4j.properties</param-value>     
   </context-param>     
   <context-param>     
      <param-name>     log4jRefreshInterval</param-name>     
      <param-value>6000</param-value>     
   </context-param>


    <listener> 
      <listener-class> 
         org.springframework.web.util.Log4jConfigListener 
      </listener-class> 
   </listener>


说明： 在上文的配置里，在上文的配置里,Log4jConfigListener会去WEB- INF/props/log4j.propeties 读取配置文件;开一条watchdog线程每60秒扫描一下配置文件的变化（这样在web服务启动后再去修改配置文件也不用重新启动web服务了）;并把 web目录的路径压入一个叫webapp.root的系统变量（webapp.root将在log4j.properties文件中使用）。

接下来是log4j.properties配置文件了，把它放在WEB-INF/props下，具体配置如下：

#log4j.rootLogger = [ level ] , appenderName, appenderName, ...
log4j.rootLogger = INFO, console, R
#level=INFO,all can be output
#console is set to be a ConsoleAppender
log4j.appender.console = org.apache.log4j.ConsoleAppender
#console have four patterns
#org.apache.log4j.HTMLLayout
#org.apache.log4j.PatternLayout
#org.apache.log4j.SimpleLayout
#org.apache.log4j.TTCCLayout
log4j.appender.console.layout = org.apache.log4j.PatternLayout
#define the output type
log4j.appender.console.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [%c]-[%p] %m%n
#file is set to output to a extra file
log4j.appender.R = org.apache.log4j.RollingFileAppender
#the absolute route of the log4j file
log4j.appender.R.File = /log.txt
#the size
log4j.appender.R.MaxFileSize = 500KB
#back up a file
log4j.appender.R.MaxBackupIndex = 1
log4j.appender.R.layout = org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss} [%c]-[%p] - %m%n

上面的配置文件说明log信息将以两种方式输出（文件和控制台），表示应用的根目录下（例如本应用名称为ABC，则log.txt的位置为tomact\webapp\ABC下）

最后在程序中想要输出log的地方加入log4j的支持

（1）引入   import org.apache.log4j.Logger

（2）声明一个logger

private static Logger logger = Logger.getLogger(ClassName.class);

（3）在程序中的相应位置加入输出信息

logger.info("用户登录:"+user.getAccount());

ok，完成了，当有登录时会在控制台和文件中同时输出log信息如下

2007-01-10 16:02:54 [com.my.web.UserAction]-[INFO] 用户登录:yangsq

ps:


作为一个流行的日志记录工具，Log4j是java开源项目中最闪亮的环节之一。究其原因，有以下几点： 

a）Log4j受大多数web应用服务器的拥护：以我目前所知，tomcat，weblogic，websphere，jboss都支持log4j。 

b）快速，功能强大：Log4J配置文件实现了输出到控制台、文件、回滚文件、发送日志邮件、输出到数据库日志表、自定义标签等全套功能。在速度上，从log4j一开始出现，注重运行的速度就一直放在首位，并且坚持不懈地进行着改进和完善。 

c）使用简单、方便：只需要导入一个简单的log4j-1.2.x.jar，然后在程序类的开头写上下面一句private final static Logger log =Logger.getLogger(ClassName.class); 


这样你就得到了一个日志对象log，可以轻松往特定目标写日志了。 


为什么需要Log4j？---项目的调试是log4j产生的内在驱动力 


原始的方法是：把信息输出到屏幕（console），利用JDK提供的System.out.println。但是，这样做的坏处是显而易见的： 


a）信息的输出不够灵活，并且繁琐。比如，要输出执行处的文件名，行数，当前时间等，println显得很原始。 


b）如果要改变输出的内容和格式，需要重新编译源程序。 


c）更严重的是，如果程序中有很多的println，会严重的影响程序的性能。 


Log4j使用的几个关键点？ 


根记录器（rootLogger），输出端（appenders）和布局（layouts） 


a）定义根记录器的格式为 


log4j.rootLogger = [ level ], appendName1, appendName2, …appendNameN。同一个记录器可有多个输出端。 

PS：level的级别（此级别可以自定义，系统默认提供了以下级别） 

◆debug//调试信息 

◆info//一般信息 

◆warn//警告信息 

◆error//错误信息 

◆fatal//致命错误信息 

上面列出的就是所谓log4j的输出级别，log4j建议只使用4个级别，它们从上到下分别为ERROR、WARN、INFO、DEBUG，假设你定义的级别是info，那么error和warn的日志可以显示而比他低的debug信息就不显示了。 


b）定义一个appender的输出目的地的格式为 

log4j.appender.appenderName = fully.qualified.name.of.appender.class。log4j提供了以下几种常用的输出目的地： 


◆org.apache.log4j.ConsoleAppender，将日志信息输出到控制台 

◆org.apache.log4j.FileAppender，将日志信息输出到一个文件 

◆org.apache.log4j.DailyRollingFileAppender，将日志信息输出到一个，并且每天输出到一个新的日志文件 

◆org.apache.log4j.RollingFileAppender，将日志信息输出到一个文件，通过指定文件的的尺寸，当文件大小到达指定尺寸的时候会自动把文件改名，如名为example.log的文件会改名为 example.log.1，同时产生一个新的example.log文件。如果新的文件再次达到指定尺寸，又会自动把文件改名为 example.log.2，同时产生一个example.log文件。依此类推，直到example.log. MaxBackupIndex， MaxBackupIndex的值可在配置文件中定义。 

◆org.apache.log4j.WriterAppender，将日志信息以流格式发送到任意指定的地方。 

◆org.apache.log4j.jdbc.JDBCAppender，通过JDBC把日志信息输出到数据库中。 

c）输出格式（布局）layout 


Log4j提供了一下几种布局： 


◆org.apache.log4j.HTMLLayout，以HTML表格形式布局 

◆org.apache.log4j.PatternLayout，可以灵活地指定布局模式 

◆org.apache.log4j.SimpleLayout，包含日志信息的级别和信息字符串 

定义一个PatternLayout布局的语句为： 

log4j.appender.stdout.layout=org.apache.log4j.PatternLayout 

log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1} - %m%n 


PS：ConversionPattern参数的格式含义 

%c 输出日志信息所属的类的全名 

%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy-MM-dd HH:mm:ss }，输出类似：2002-10-18- 22：10：28 

%f 输出日志信息所属的类的类名 

%l 输出日志事件的发生位置，即输出日志信息的语句处于它所在的类的第几行 

%m 输出代码中指定的信息，如log(message)中的message 

%n 输出一个回车换行符，Windows平台为“\r\n”，Unix平台为“\n” 

%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL。如果是调用debug()输出的，则为DEBUG，依此类推 

%r 输出自应用启动到输出该日志信息所耗费的毫秒数 

%t 输出产生该日志事件的线程名 

log4j的配置文件:log4j.properties 

一个log4j.properties文件示例 


log4j.rootLogger=INFO, stdout, logfile

log4j.appender.stdout=org.apache.log4j.ConsoleAppender

log4j.appender.stdout.layout=org.apache.log4j.PatternLayout

log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - <%m>%n

log4j.appender.logfile=org.apache.log4j.RollingFileAppender

log4j.appender.logfile.File=/webserver/specialTraining3/wangzj.log

log4j.appender.logfile.MaxFileSize=51200KB

log4j.appender.logfile.MaxBackupIndex=3

log4j.appender.logfile.layout=org.apache.log4j.PatternLayout

log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - <%m>%n 


       
