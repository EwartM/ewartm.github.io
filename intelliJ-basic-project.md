---
layout: default
published: true
---

# [](#header-1)Creating a new GWT & GAE project with IntelliJ IDEA

> Follow these steps in IntelliJ IDEA to create a GWT app running on GAE standard environment  
> We'll use Gradle to manage dependencies

1. Java 7
1. GWT2.8.0beta1
1. GAE 1.9.42
1. Gradle
1. IntelliJ IDEA Ultimate 2016.3

[Github version after completing this tutorial](https://github.com/EwartM/MyAppName/tree/0997f4c71a2e3f1ecd885414210036f7e1b13808)

#### [](#header-2)Prerequisite

[Enable Gradle support in IntelliJ IDEA](https://www.jetbrains.com/help/idea/2016.3/gradle.html#d285963e15)

#### [](#header-2)Create the project and parent module

Create a new project in IntelliJ IDEA.

![]({{ site.baseurl }}/assets/images/Screen1.png)

Select Gradle. Uncheck all boxes.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.51.40%20PM.png)

Choose your namespace & application name.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.52.25%20PM.png)

Use Gradle wrapper and **set JAVA to 1.7 (GAE standard does not support Java 8 yet - March 2017)**

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.52.45%20PM.png)

Name the parent module that will contain the dependencies and app module.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.53.01%20PM.png)

IDEA has created a blank build.gradle file for us.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.53.41%20PM.png)

Modify the default build.gradle with the dependencies you plan to use (you can add / remove later as required). **Do not add Java, GWT or GAE dependencies as the app module will include those.**   

Below is an example build.gradle file

```XML
group 'com.example'
version '1.0-SNAPSHOT'

apply plugin: 'war'

repositories {
    mavenCentral()
}
// Dependency Versions
ext{
    gaeVer = '1.9.42'
    requestFactoryVer = '2.8.0-beta1'
    gwtpVer = '1.5.3'
    jbcryptVer = '0.3m'
    jodaVer = '2.9.1'
    guiceVer = '3.0'
    hibernateVer = '4.1.0.Final'
    sl4jVer = '1.6.1'
    gwtqueryVer = '1.4.3'
}
dependencies {
    compile group: 'com.google.web.bindery', name: 'requestfactory-server', version: requestFactoryVer
    compile group: 'com.google.web.bindery', name: 'requestfactory-apt', version: requestFactoryVer
    compile group: 'com.gwtplatform', name: 'gwtp-mvp-client', version: gwtpVer
    compile group: 'org.mindrot', name: 'jbcrypt', version: jbcryptVer
    compile group: 'joda-time', name: 'joda-time', version: jodaVer
    compile group: 'com.google.inject', name: 'guice', version: guiceVer
    compile group: 'com.google.inject.extensions', name: 'guice-assistedinject', version: guiceVer
    compile group: 'com.google.inject.extensions', name: 'guice-servlet', version: guiceVer
    compile group: 'org.hibernate', name: 'hibernate-validator-annotation-processor', version: hibernateVer
    compile(group: 'org.hibernate', name: 'hibernate-validator', version: hibernateVer) {
        exclude(module: 'jaxb-api')
        exclude(module: 'jaxb-impl')
        exclude(module: 'slf4j-api')
    }
    compile(group: 'org.hibernate', name: 'hibernate-validator', version: hibernateVer, classifier:'sources') {
        exclude(module: 'jaxb-api')
        exclude(module: 'jaxb-impl')
        exclude(module: 'slf4j-api')
    }
    compile group: 'org.slf4j', name: 'slf4j-log4j12', version: sl4jVer
    compile group: 'com.googlecode.gwtquery', name: 'gwtquery', version: gwtqueryVer

    testCompile 'junit:junit:4.11'
}
```

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.54.52%20PM.png)

Open the Gradle tab and select **build** to copy the libraries to the project.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.55.50%20PM.png)

#### [](#header-2)Create the application module and link it's dependencies

Now let's add the application module.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.56.15%20PM.png)

Check Google App Engine and browse to your local GAE SDK. Note that GAE standard only supports up to GAE SDK 1.9.42

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.56.51%20PM.png)

Check Google Web Toolkit and browse to your local GWT SDK. Note that GAE standard only supports up to GWT2.8.0beta1 

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.57.20%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.57.45%20PM.png)

When done right-click the App module and select Module Settings.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.58.49%20PM.png)

Click **+** and then **Library** to add the dependencies from our parent module (loaded by the build.gradle) that our App module will need.  

Add all libraries except the Application Server Libraries.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.59.35%20PM.png)

Now you'll see the libraries listed as part of our App build.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.00.00%20PM.png)

Next, edit the web.xml file found in App/web/WEB-INF
```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <!-- Default page to serve -->
    <welcome-file-list>
        <welcome-file>app.html</welcome-file>
    </welcome-file-list>
    <servlet>
        <servlet-name>app appService</servlet-name>
        <servlet-class>com.example.app.server.appServiceImpl</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>app appService</servlet-name>
        <url-pattern>/app/appService</url-pattern>
    </servlet-mapping>
</web-app>
```

#### [](#header-2)Debug the application on a local server

Select **debug** with the 'app' task to start a local server where we can test that App works.

![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-08 at 12.12.12 PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.03.01%20PM.png)

#### [](#header-2)Test server breakpoints and code update

You should be able to set a breakpoint in the server side code.  
The client code doesn't support breakpoints for some reason. As a workaround you can use Chrome > More Tools > Developer Tools to debug the front-end.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.03.23%20PM.png)

Click the button to resume after hitting a breakpoint.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.04.15%20PM.png)

Make some changes to the code and save.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.04.36%20PM.png)

Refresh the browser tab.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.05.19%20PM.png)

#### [](#header-2)Debug the application on a local GAE instance

Select **debug** with the 'AppEngine Dev 1.9.42' task to start a local GAE server where we can test that App works.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.00.33%20PM.png)

Add '/app.html' to the path and select App:war exploded as the artifact to deploy.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.01.01%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.01.25%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.01.35%20PM.png)

If all goes well you should have 2 URLs in the console. The first is where our App is served. The second gives access to the local Datastore instance.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.02.46%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.03.01%20PM.png)

#### [](#header-2)Deploy to GAE standard

Create a new Configuration to deploy the App to the Google App Engine cloud. 

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.05.55%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.06.13%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.07.02%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.06%20PM.png)

Run the **deploy** task. You'll need a GAE project set up to receive the application. 

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.21%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.29%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.50%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.12.45%20PM.png)
    
    
[Next we'll add the GWTP MVP framework](add-gwtp-mvp-architecture)

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)
