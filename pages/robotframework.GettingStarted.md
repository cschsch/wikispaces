---
title: robotframework.GettingStarted
---
## Under OS X
### robotframework
* Downloaded source from: <http://robotframework.googlecode.com/files/robotframework-2.1.tar.gz>
* Extracted it into ~/src/, which created ~/src/robotframework-2.1
* Verified that: pybot --version ran successfully (it did)
* Verified that: jybot --verion ran successfully (it did not)

### jython
* Downloaded jython 2.5 from: <http://downloads.sourceforge.net/jython/jython_installer-2.5.0.jar> (note, as of when I did this, 2.1 was OK, but not 2.1.1, the docs said that 2.5 should be OK)
* Executed jython install
{% highlight terminal %}
java -jar jython_installer-2.5.0.jar
{% endhighlight %}
* Put it in ~/bin, which created ~/bin/jython2.5.0
* Added ~/bin/jython2.5.0 to path in my .zlogin file
* Killed my terminal screens and verified jython was now available
* Verified that: jybot --verion ran successfully (it did)
 
