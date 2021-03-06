---
title: Commons_Logging_and_Log4j_Config
---
{% include nav prev="Tool_Setup_and_Configuration_Notes" %}
{% include toc %}

When I started using [Spring](http://www.springframework.org/), I need to include some logging and basic configuration. This page gives the details for the entire setup I'm using.

## Log 4j
* Download log4j from [here](http://logging.apache.org/site/binindex.cgi) (in my case, I downloaded the [Spring](http://www.springframework.org/) jar with dependencies from [here](http://sourceforge.net/project/showfiles.php?group_id=73357&package_id=173644), which includes Log4j).
* Extract the jar somewhere
* Add the jar to your Eclipse project
* Create a simple configurator, see LogginConfiguration.java, and a simple configuration file, see [log4j.properties](#log4j).


{: #LoggingConfiguration}
## LoggingConfiguration.java
{% highlight java %}
01: package loggingutil;
02: 
03: import java.net.URL;
04: 
05: import org.apache.log4j.BasicConfigurator;
06: import org.apache.log4j.PropertyConfigurator;
07: 
08: public final class LoggingConfiguration {
09:     private static final String LOG4J_PROPS = "loggingutil/log4j.properties";
10: 
11:     private LoggingConfiguration() {
12:         // utility class
13:     }
14: 
15:     static {
16:         BasicConfigurator.configure();
17:         URL propertiesUrl = LoggingConfiguration.class.getClassLoader().getResource(LOG4J_PROPS);
18:         if (propertiesUrl == null) {
19:             getLoggerFor(LoggingConfiguration.class).fatal(
20:                     String.format("Unable to configure logger using: %s", LOG4J_PROPS));
21:         } else {
22:             PropertyConfigurator.configure(propertiesUrl);
23:         }
24:     }
25: 
26:     public static ILogger getLoggerFor(final Class clazz) {
27:         return new Logger(org.apache.log4j.Logger.getLogger(clazz));
28:     }
29: 
30:     public static void initialize() {
31:         // an empty method, when a class uses this method it causes the class
32:         // to get loaded, which runs its static initializer that reads
33:         // additional configuration.
34:     }
35: 
36: }
{% endhighlight %}
### Interesting Lines
^
|-|-|
|Line|Description|
|11 - 13|I'm using [Checkstyle](http://eclipse-cs.sourceforge.net/) and [PMD](http://pmd.sourceforge.net/integrations.html#eclipse). One of them has a rule that suggests classes with all static methods should have a private constructor to disallow instantiation.|
|15 - 24|When loading this class, perform basic Log4j configuration, line 17. In addition, Spring, by default, produces quite a bit of output. The output is useful but I generally don't want it unless I'm debugging a problem. So I load a simple properties file that sets the default logging for [Spring](http://www.springframework.org/) to WARN. If I need to switch to DEBUG, I simply edit [log4j.properties](#log4j).|
|17|Generate a URL for the resource named LOG4J_PROPS. I use the same classloader used for this class, and ask it to find the named resource. This returns either a URL to the location of the file or null.|
|18 - 21|If the resource is not found (null returned), log a fatal error to the console and do nothing else. Hopefully someone will notice it. I don't want this class' static initializer to fail because if it does it will cause classes that use it to fail since the class will not be loaded. This is an important point to make, generally you should not allow a static initializer to fail because what you'll see is an error about the class not being found but the actual error was lost when the static initialzier failed.|
|19 - 20|If I'm unable to locate the URL, get a logger and log a fatal message.|
|20|Use the new method, String.format, to format a string using old C-Style format strings.|
|22|I did find the URL, go ahead a process the contents of this property file.|
|26 - 28|Just in case I want to get a logger, I have a simple method that will give me an [ILogger](Commons_Logging_and_Log4j_Config#ILogger) for a given class. Notice that I'm not using Sun's or Lor4J's Logger. Why? I change the interface to support both a variable number of parameters and automatic log level checking. So no place in my code beyond this configuration utility is aware of Log4J. I know I'm going to use Log4J so is there any value in wrapping it? In this case I am not simply wrapping it by actually adapting its interface.|
|30 - 34|The comment says it all. If you want to look like you are initializing the logging infrastructure, call initialize. It's an empty method, but by referencing it, the class will get loaded and the logger will get initialized because of the static initializer on lines 12 - 15.|

{: #log4j}
## log4j.properties
This file resides in the same directory as the source file for [LogginConfiguration.java](#LoggingConfiguration).

{% highlight java %}
01: log4j.logger.org.springframework=WARN
{% endhighlight %}
### Interesting Lines
^
|-|-|
|Line|Description|
|01|This sets the logging level from its default, DEBUG, to WARN. I did this to reduce the output produced by Spring. I've occasionally turned it back on to DEBUG to trace through things. However, I generally prefer my console to have little if any output so that what's there is something I know I need to pay attention to.|

{: #ILogger}
## ILogger.java
Yes this is yet another logger wrapper, or is it? I use Log4J. Unfortunately it does not have an interface that uses the features offered by Java 5 so instead of using Lof4J directly, I have returned this interface that publishes what I think is a better interface.

{% highlight java %}
01: package loggingutil;
02: 
03: public interface ILogger {
04:     ILogger debug(final String formatString, final Object... params);
05: 
06:     ILogger debug(final Throwable t, final String formatString, final Object... params);
07: 
08:     ILogger warn(final String formatString, final Object... params);
09: 
10:     ILogger warn(final Throwable t, final String formatString, final Object... params);
11: 
12:     ILogger info(final String formatString, final Object... params);
13: 
14:     ILogger info(final Throwable t, final String formatString, final Object... params);
15: 
16:     ILogger error(final String formatString, final Object... params);
17: 
18:     ILogger error(final Throwable t, final String formatString, final Object... params);
19: 
20:     ILogger fatal(final String formatString, final Object... params);
21: 
22:     ILogger fatal(final Throwable t, final String formatString, final Object... params);
23: }
{% endhighlight %}
### Interesting Lines
There are not really any specifically interesting lines. Notice that every method's last parameter is a variable number of parameters. Take a look below at the implementation of this class.

----
{: #Logger}
## Logger.java
{% highlight java %}
01: package loggingutil;
02: 
03: import org.apache.log4j.Level;
04: 
05: public class Logger implements ILogger {
06:     private final org.apache.log4j.Logger wrappedLogger;
07: 
08:     public Logger(final org.apache.log4j.Logger logger) {
09:         this.wrappedLogger = logger;
10:     }
11: 
12:     public ILogger debug(final String formatString, final Object... params) {
13:         if (wrappedLogger.isDebugEnabled()) {
14:             wrappedLogger.debug(String.format(formatString, params));
15:         }
16:         return this;
17:     }
18: 
19:     public ILogger debug(final Throwable t, final String formatString, final Object... params) {
20:         if (wrappedLogger.isDebugEnabled()) {
21:             wrappedLogger.debug(String.format(formatString, params), t);
22:         }
23:         return this;
24:     }
25: 
26:     public ILogger warn(final String formatString, final Object... params) {
27:         if (wrappedLogger.isEnabledFor(Level.WARN)) {
28:             wrappedLogger.warn(String.format(formatString, params));
29:         }
30:         return this;
31:     }
32: 
33:     public ILogger warn(final Throwable t, final String formatString, final Object... params) {
34:         if (wrappedLogger.isEnabledFor(Level.WARN)) {
35:             wrappedLogger.warn(String.format(formatString, params), t);
36:         }
37:         return this;
38:     }
39: 
40:     public ILogger info(final String formatString, final Object... params) {
41:         if (wrappedLogger.isEnabledFor(Level.INFO)) {
42:             wrappedLogger.info(String.format(formatString, params));
43:         }
44:         return this;
45:     }
46: 
47:     public ILogger info(final Throwable t, final String formatString, final Object... params) {
48:         if (wrappedLogger.isEnabledFor(Level.INFO)) {
49:             wrappedLogger.info(String.format(formatString, params), t);
50:         }
51:         return this;
52:     }
53: 
54:     public ILogger error(final String formatString, final Object... params) {
55:         if (wrappedLogger.isEnabledFor(Level.ERROR)) {
56:             wrappedLogger.error(String.format(formatString, params));
57:         }
58:         return this;
59:     }
60: 
61:     public ILogger error(final Throwable t, final String formatString, final Object... params) {
62:         if (wrappedLogger.isEnabledFor(Level.ERROR)) {
63:             wrappedLogger.error(String.format(formatString, params), t);
64:         }
65:         return this;
66:     }
67: 
68:     public ILogger fatal(final String formatString, final Object... params) {
69:         if (wrappedLogger.isEnabledFor(Level.FATAL)) {
70:             wrappedLogger.fatal(String.format(formatString, params));
71:         }
72:         return this;
73:     }
74: 
75:     public ILogger fatal(final Throwable t, final String formatString, final Object... params) {
76:         if (wrappedLogger.isEnabledFor(Level.FATAL)) {
77:             wrappedLogger.fatal(String.format(formatString, params), t);
78:         }
79:         return this;
80:     }
81: }
{% endhighlight %}
### Interesting Lines

^
|-|-|
|Line|Description|
|6|This class holds a reference to a Log4J logger instance. It publishes a different interface and adapts the new interface on to the Log4J interface.|
|12 - 17|A typical problem with logging is that of string concatenation. A way to avoid this is to check the logging level performing any string concatenation. That makes for ugly code. With variable arguments to methods, we can pass in a format string and the parameters to be sent into the format string, check the logging level and only actually perform the string formatting if the logging level is enabled. Each of these methods does exactly that.|
|12|Take in a [format string](http://java.sun.com/j2se/1.5.0/docs/api/java/util/Formatter.html#syntax) and a variable number of arguments.|
|13|Is debug output enables for my contained logger?|
|14|Degugging is enabled, use the static method [String.format](http://java.sun.com/j2se/1.5.0/docs/api/java/lang/String.html#format(java.lang.String,%20java.lang.Object...)) passing in the variables arguments received by this method into the format method. The result of that is sent to the underlying Log4J logger.|
|16|Return myself to allow for method chaining.|
|19|Notice that Throwable is the **first** parameter. In the Log4J methods, the Throwable parameter is last. Since we are using variable arguments, we cannot put the Throwable parameter last so we instead make it the first one.|

All of the methods follow the same pattern. Rather that writing the following code, which I've seen:

{% highlight java %}
    Logger myLogger = ...; // get a Log4J Logger
    ...
    if(myLogger.isDebugEnabled()) {
       myLogger.debug("User Name: " + userName + " is not authorized for this operation.");
    }
{% endhighlight %}

Of course, you might want to simplify this code to the following, and I've seen this even more often:
{% highlight java %}
    Logger myLogger = ...; // get a Log4J Logger
    ...
    myLogger.debug("User Name: " + userName + " is not authorized for this operation.");
{% endhighlight %}

The problem with this is that we perform the work of concatenating three strings even if the logger does not have debug level messages enabled. So the first form is ugly but better. The second form looks better, but performs potentially unnecessary work.

This adapted interface gives us the following:
{% highlight java %}
    Logger myLogger = ...; // get an ILogger
    ...
    myLogger.debug("User Name: %s is not authorized for this operation.", userName);
{% endhighlight %}

This looks like the cleaner second version but it performs like the better first version.

This example is not complete. It does not publish the logging levels, which really is still necessary. Consider the following example that is NOT fixed by this interface:
{% highlight java %}
    Logger myLogger = ...; // get an ILogger
    ...
    myLogger.debug("User Name: %s is not authorized for this operation.", getUserNameFromJndi());
{% endhighlight %}
Let’s say that the method getUserNameFromJndi() takes a long time. In this example, we called the method before calling the debug() method, so we did this extra work potentially unnecessarily.

{% include nav prev="Tool_Setup_and_Configuration_Notes" %}
