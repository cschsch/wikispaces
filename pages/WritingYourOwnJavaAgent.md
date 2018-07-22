[[image:Back.gif link="JavaAgent"]]
A Java Agent, once registered with the class loader, has a single method:
```java
public class SkeletonClassFileTransformer implements ClassFileTransformer {
    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined,
            ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException {
        return null;
    }
}
```

A Java Agent is given the option of modifying the array of bytes representing the class before the class loader finishes the class loading process. Here is a [[http://java.sun.com/docs/books/jvms/second_edition/html/ConstantPool.doc.html|specification of class loading]]. In a nutshell, the bytes are given to the Java Agent after the class loader has retrieved them but before Linking. Your Java Agent can create a new byte array in a [[http://java.sun.com/docs/books/jvms/first_edition/html/ClassFile.doc.html|valid class file format]] and return it or, if it is not performing a transformation, return null.

Here is an example that simply prints a message like the following:
```terminal
Class: StringCoding in: java/lang
Class: StringCoding$CharsetSE in: java/lang
Class: StringCoding$StringEncoder in: java/lang
Class: Main in: schuchert/agent
Class: Shutdown in: java/lang
Class: Shutdown$Lock in: java/lang
```
[[#ClassAndPackageNamePrintingClassFileTransformer]]
```java
package schuchert.agent;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.security.ProtectionDomain;

public class ClassAndPackageNamePrintingClassFileTransformer implements ClassFileTransformer {

    public byte[] transform(ClassLoader loader, String fullyQualifiedClassName, Class<?> classBeingRedefined,
            ProtectionDomain protectionDomain, byte[] classofileBuffer) throws IllegalClassFormatException {
        String className = fullyQualifiedClassName.replaceAll(".*/", "");
        String pacakge = fullyQualifiedClassName.replaceAll("/[a-zA-Z$0-9_]*$", "");
        System.out.printf("Class: %s in: %s\n", className, pacakge);
        return null;
    }
}
```
How do you get all of this to work? Here's what you'll need to do:
# Create an Implementation of [[http://java.sun.com/j2se/1.5.0/docs/api/java/lang/instrument/ClassFileTransformer.html|ClassFileTransformer]] 
# Create a class with a [[http://java.sun.com/javase/6/docs/api/java/lang/instrument/package-summary.html|premain method]] (could be in the first class, but but that would violate [[http://en.wikipedia.org/wiki/Single_responsibility_principle|The Single Responsibility Principle]]).
# Create jar file
# Star the VM with a command line parameter.

And, of course, are the details for each of those steps.
## Create an Implementation of [[http://java.sun.com/j2se/1.5.0/docs/api/java/lang/instrument/ClassFileTransformer.html|ClassFileTransformer]]
The [[JavaAgent#ClassAndPackageNamePrintingClassFileTransformer|class above]] is a complete example of a class that can "transform" a just-loaded class. By itself, it really does not do much. However, if you'd like to perform some custom transformation, you could create a new byte array, add in some Java Bytecodes and then return that class instead.

Why would you do this? Here are a few examples:
# You're adding logging code to a class
# Custom implementation of AOP
# Instrument a class to better thread-based testing (my reason for looking into this in the first place)
# Etc.

## Create a class with a [[http://java.sun.com/javase/6/docs/api/java/lang/instrument/package-summary.html|premain method]]
The class file transformer is not directly added to the class loader. Instead, you create another class with a method called premain that instantiates the class and registers it. Here is a complete example:
```java
package schuchert.agent;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.Instrumentation;

public class RegisterMyClassFileTransformer {
    public static void premain(String agentArguments, Instrumentation instrumentation) {
        instrumentation.addTransformer(new ClassAndPackageNamePrintingClassFileTransformer());
    }
}
```

Why this indirection? I'm not exactly sure. However, I use it to write the registrar once and reuse it across projects. The act of registration is the same, what I want to register varies by project. The Registrar.jar file you'll download is essentially a simple "component" that allows for registration of a ClassFileTransformer you create in your project by specifying a system property. 

## Create Jar File
When you add a class file transformer to the class loader, you must specify the name of a jar file. You cannot simply name a class in the classpath. So if the ClassAndPackageNamePrintingClassFileTransformer is in the class path, then you need to add the class RegisterMyClassFileTransformer to a jar file and add a manifest file to specify it.

The jar file needs to have the following structure:
```terminal
Top Of Jar File
    <sub directory>com
        <sub directory>javaagent
            <file>RegisterMyClassFileTransformer.class
    <sub directory>META-INF
        <file>MANIFEST.MF
```
The contents of the MANIFEST.MF file, at a minimum, would be:
```terminal
Manifest-Version: 1.0 
Premain-Class: com.javaagent.RegisterMyClassFileTransformer
```

NOTE: If you happen to download the Eclipse project mentioned [[AnExampleJavaAgent|here]], you can simply find the **RecreateJar.jardesc** file in the Eclipse project, right-click and select **Create JAR**.
## Start the VM
Finally, we need to star the VM:
```terminal
java -javaagent:MyJarFile.jar <A Regular Class With A Main>
```
[[image:Back.gif link="JavaAgent"]]