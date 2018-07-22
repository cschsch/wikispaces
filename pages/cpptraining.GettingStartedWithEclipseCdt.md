[[cpptraining#gettingfirsttestrunning|<--Back]]

# Overview
This instructions help you get started using Eclipse and the CDT to develop C++ systems under Windows.

# Many Thanks To
After many tries and false paths, I finally got a fully working environment fairly easily. Thank you to [[http://cdtdoug.blogspot.com/|Doug Schaefer]] ([[http://twitter.com/dougschaefer|Twitter]]), Eclipse CDT Project Lead.

These instructions are essentially a duplication of: [[http://code.google.com/a/eclipselabs.org/p/wascana/|Wascana Eclipse C/C++ IDE for Windows Developers]].

# Steps
Note: If you install the 64-bit JDK, install the 64-bit Eclipse. Same with 32-bit.

* Install a JDK (this is to run Eclipse): [[@http://java.sun.com/javase/downloads/widget/jdk6.jsp|Jdk 1.6 Install]]
* Download and install the Helios version of eclipse with the Eclipse CDT: [[@http://www.eclipse.org/downloads/packages/eclipse-ide-cc-developers-includes-incubating-components/indigosr1|Helios]]
* Add the wascana plugin from the following Eclipse update site: [[@http://svn.codespot.com/a/eclipselabs.org/wascana/repo| Wascana Update Site]]
** Start eclipse
** Go to// **Help::Install New Software...**//
** Paste http://svn.codespot.com/a/eclipselabs.org/wascana/repo/ in the// **Work with:**// box
** Click// **Add**//
** In the dialog, provide a name (I used// **wascana**//))
** Click// **OK**//
** Eventually you'll see under the **Name** column in the middle of the dialog "Wascana C/C++ Developer Tools for Windows"
** Click the box next to it to make a check appear.
** Click// **Next**//
** When the **Install Details** window appears, click// **Next**//
** When the **Review Licenses** window appears, select the "I Accept the terms of the License Agreements" and click// **Finish**//
** After the update is downloaded, you'll be asked to apply the updates. Do so by selecting// **Restart Now**//.

That's it, works quite well. This gives support for compiling and debugging C++ applications in Eclipse.

# Verifying It Works
* Create a new C++ project
* Select the Hello World Template
* Build and run it, notice the output in the console window
* Debug it, you should see it pause just before beginning main()

[[cpptraining#gettingfirsttestrunning|<--Back]]