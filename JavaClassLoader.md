Java ClassLoader
Java ClassLoader is one of the crucial but rarely used components of Java in Project Development. Personally I have never extended ClassLoader in any of my projects but the idea of having my own ClassLoader that can customize the Java Class Loading thrills me.
Java ClassLoader
This article will provide an overview of Java ClassLoader and then move forward to create a custom ClassLoader in Java.
What is Java ClassLoader?
We know that Java Program runs on Java Virtual Machine (JVM). When we compile a Java Class, it transforms it in the form of bytecode that is platform and machine independent compiled program and store it as a .class file. After that when we try to use a Class, Java ClassLoader loads that class into memory.
There are three types of built-in ClassLoader in Java:
1.	Bootstrap Class Loader – It loads JDK internal classes, typically loads rt.jar and other core classes for example java.lang.* package classes
2.	Extensions Class Loader – It loads classes from the JDK extensions directory, usually $JAVA_HOME/lib/ext directory.
3.	System Class Loader – It loads classes from the current classpath that can be set while invoking a program using -cp or -classpath command line options.
Java ClassLoader are hierarchical and whenever a request is raised to load a class, it delegates it to its parent and in this way uniqueness is maintained in the runtime environment. If the parent class loader doesn’t find the class then the class loader itself tries to load the class.
Lets understand this by executing the below java program:
ClassLoaderTest.java
Output of the above java classloader example program is:
As you can see that java.util.HashMap ClassLoader is coming as null that reflects Bootstrap ClassLoader whereas DNSNameService ClassLoader is ExtClassLoader. Since the class itself is in CLASSPATH, System ClassLoader loads it.
When we are trying to load HashMap, our System ClassLoader delegates it to the Extension ClassLoader, which in turns delegates it to Bootstrap ClassLoader that found the class and load it in JVM.
The same process is followed for DNSNameService class but Bootstrap ClassLoader is not able to locate it since its in $JAVA_HOME/lib/ext/dnsns.jar and hence gets loaded by Extensions Class Loader. Note that Blob class is included in the MySql JDBC Connector jar (mysql-connector-java-5.0.7-bin.jar) that I have included in the build path of the project before executing it and its also getting loaded by System Class Loader.
One more important point to note is that Classes loaded by a child class loader have visibility into classes loaded by its parent class loaders. So classes loaded by System ClassLoader have visibility into classes loaded by Extensions and Bootstrap ClassLoader.
If there are sibling class loaders then they can’t access classes loaded by each other.
Why write a Custom ClassLoader in Java?
Java default ClassLoader can load files from local file system that is good enough for most of the cases. But if you are expecting a class at the runtime or from FTP server or via third party web service at the time of loading the class then you have to extend the existing class loader. For example, AppletViewers load the classes from remote web server.
How does Java ClassLoader Work?
When JVM requests for a class, it invokes loadClass function of the ClassLoader by passing the fully classified name of the Class.
loadClass function calls for findLoadedClass() method to check that the class has been already loaded or not. It’s required to avoid loading the class multiple times.
If the Class is not already loaded then it will delegate the request to parent ClassLoader to load the class.
If the parent ClassLoader is not finding the Class then it will invoke findClass() method to look for the classes in the file system.
Java Custom ClassLoader
We will create our own ClassLoader by extending ClassLoader class and overriding loadClass(String name) method. If the name will start from com.journaldev i.e our sample classes package then we will load it using our own class loader or else we will invoke the parent ClassLoader loadClass() method to load the class.
The project structure will be like the below image:
 
CCLoader.java: This is our custom class loader with below methods.
1.	private byte[] loadClassFileData(String name):
This method will read the class file from file system to byte array.
2.	private Class getClass(String name)
This method will call the loadClassFileData() function and by invoking the parent defineClass() method, it will generate the Class and return it.
3.	public Class loadClass(String name):
This method is responsible for loading the Class. If the class name starts with com.journaldev (Our sample classes) then it will load it using getClass() method or else it will invoke the parent loadClass function to load it.
4.	public CCLoader(ClassLoader parent):
This is the constructor which is responsible for setting the parent ClassLoader.
CCLoader.java
CCRun.java:
This is our test class with main function where we are creating object of our ClassLoader and load sample classes using its loadClass method. After loading the Class, we are using Java Reflection API to invoke its methods.
CCRun.java
Foo.java and Bar.java:
These are our test classes that is getting loaded by our custom classloader. They also have a printCL() method that is getting invoked to print the ClassLoader that has loaded the Class. Foo class will be loaded by our custom class loader which in turn uses Bar class, so Bar class will also be loaded by our custom class loader.
Foo.java
Bar.java
Java Custom ClassLoader Execution Steps
First of all we will compile all the classes through command line. After that we will run CCRun class by passing three arguments. The first argument is the fully classified name for Foo class that will get loaded by our class loader. Other two arguments are passed along to the Foo class main function and Bar constructor. The execution steps with output will be like below.
$
If you look into the output carefully, first its trying to load com.journaldev.cl.Foo class but since its extending java.lang.Object class, its trying to load it first and the request it coming to CCLoader loadClass method that is delegating it to the parent class. So the parent class loaders are loading the Object, String and other java classes. Our ClassLoader is only loading Foo and Bar class from the file system that is getting clear when we invoke their printCL() function.
Note that we can change the loadClassFileData() functionality to read the byte array from FTP Server or by invoking any third party service to get the class byte array on the fly.
I hope that the article will be useful in understanding Java ClassLoader working and how we can extend it to do a lot more that just taking it from file system.
Updated from comment by m29
We can make our custom class loader as the default one when JVM starts by using Java Options.
For example, I will run the ClassLoaderTest program once again after providing java class loader option.
As you can see that CCLoader is loading the ClassLoaderTest class because its in com.journaldev package.
Mac OS X 10.6.4 Issue with ClassLoader Java Options
If you are working on Mac OS the above execution can throw some exceptions but it will execute successfully.
Refer this email archive where Tim Quinn is also facing the same issue.
That’s all for Java ClassLoader and java custom class loader example.
FILED UNDER: INTERVIEW QUESTIONS, JAVA

