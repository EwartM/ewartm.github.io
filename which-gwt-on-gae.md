---
layout: default
---

# [](#header-1)Choosing a version of GWT for GAE

As of March 2017, the Google App Engine standard environment still doesn't support Java 8  
To use Java 8 and the newer GWT libraries we could use GAE flexible environment  
Below we'll look at the pro's and con's of each approach  

#### [](#header-2)GAE Standard

#### [](#header-3)Cons
- Java 7 only. Google have been talking about GAE Java 8 support since 2015...
  - Old libraries only
    - GAE max 1.9.42
    - GWT max 2.8.0-beta1
    - Vaadin Polymer Elements max 1.2.3.0
    - GWTP max 1.5.3
    - GWT D3JS max 1.2.0 ?
  - Possible workarounds to use Java 8 libraries
    - Use JDK 8 and retrolambda: https://github.com/orfjackal/retrolambda
    - Split your code into 2 projects/modules, compiled in 2 phases
     - https://discuss.gradle.org/t/building-a-multi-module-gwt-project-with-gradle/3707
     - https://blog.oio.de/2014/09/05/avoid-multi-module-builds-gradle/
 
#### [](#header-2)GAE Flexible (aka managed VM)
 1.	Pros
 2.	Supports Java 8
 2.	Faster (I haven't really tested this but on the surface my login goes much faster on flexible)
 2.	New versions of libraries
 1.	Cons
 2.	No free quota
 3.	To reduce costs make sure app.yaml specifies 1 CPU 
 2.	No EU hosting [yet]
 2.	No task queuing in GAE Flex (unlike GAE standard)
 3.	https://groups.google.com/forum/#!topic/google-appengine/C9Sv4XkyHG4
 3.	https://groups.google.com/forum/?fromgroups#!searchin/objectify-appengine/flexible%7Csort:relevance/objectify-appengine/_8qQicEYg7E/0_msm1v3AAAJ
 2.	Manually manage static files – to blob?
 2.	Objectify doesn't work because it uses GAE standard API (big maybe on migration from Objectify dev)
 2.	Datastore connection 
 3.	Use Cloud Datastore REST api (https://github.com/GoogleCloudPlatform/google-cloud-java/tree/master/google-cloud-datastore)
 3.	via Catatumbo (Objectify doesn't work on GAE Flex [yet])
 2.	Docker file and app.yaml


app.yaml
runtime: custom  
env: flex
threadsafe: True
resources:
  cpu: 1



Dockerfile
FROM gcr.io/google-appengine/jetty9
ADD /path/to/your/war $JETTY_BASE/webapps/root
WORKDIR $JETTY_BASE
RUN java -jar $JETTY_HOME/start.jar --approve-all-licenses --add-to-startd=jmx,stats,hawtio \
 && chown -R jetty:jetty $JETTY_BASE


 3.	The line 'ADD /out/artifacts/App_war_exploded $JETTY_BASE/webapps/root' tells docker to copy the contents of your exploded web app folder to the server webapps folder for serving
 2.	Flex Deployment
 3.	Custom Docker image
 4.	cd to app directory
 4.	'cloud init' to set correct project to upload to
 4.	'docker build -t popi-docker .' (build every time docker file changes)
 4.	'gcloud app deploy'
 4.	optional 
 5.	RUN 'docker run -ti -p 8080:8080 --rm --name popi-docker popi-docker'
 5.	DELETE ALL 'docker rmi $(docker images -q)'
 3.	Direct Gradle build using built in Container 
 4.	I discarded this option due to lack of documentation



1. Java 7
1. GWT2.8.0beta1
1. GAE 1.9.42
1. Gradle 3.1
1. IntelliJ IDEA Ultimate 2016.3

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
    gwtVer = '2.8.0-beta1'
    objectifyVer = '5.1.9'
    gwtpVer = '1.5.3'
    jbcryptVer = '0.3m'
    jodaVer = '2.9.1'
    guiceVer = '3.0'
    hibernateVer = '4.1.0.Final'
    sl4jVer = '1.6.1'
    vaadinPolymerVer = '1.2.3.0'
    gwtqueryVer = '1.4.3'
    d3Ver = '1.2.0'
}

dependencies {
    compile group: 'com.google.web.bindery', name: 'requestfactory-server', version: gwtVer
    compile group: 'com.google.web.bindery', name: 'requestfactory-apt', version: gwtVer
    compile group: 'com.googlecode.objectify', name: 'objectify', version: objectifyVer
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
    compile group: 'com.vaadin.polymer', name: 'vaadin-gwt-polymer-elements', version: vaadinPolymerVer
    compile group: 'com.googlecode.gwtquery', name: 'gwtquery', version: gwtqueryVer
    compile group: 'com.github.gwtd3', name: 'gwt-d3-api', version: d3Ver

    testCompile 'junit:junit:4.11'
}
```





![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

