---
layout: default
published: true
---

# [](#header-1)Choosing a version of GWT for GAE

As of March 2017, the GAE standard environment still doesn't support Java 8  
To use Java 8 and the newer GWT libraries we could use GAE flexible environment  
Below we'll look at the pros and cons of each approach  

#### [](#header-1)GAE Standard
#### [](#header-3)Pros  
- **Straightforward deploy** in IntelliJ IDEA
- **Free App Engine quota**
- **EU hosting**
- Full **datastore integration** with task queuing and Objectify ORM

#### [](#header-3)Cons
Java 7 only. Google have been talking about GAE Java 8 support since 2015...
- **Older libraries** only
    	- GAE max 1.9.42
    	- GWT max 2.8.0-beta1
    	- Vaadin Polymer Elements max 1.2.3.0
    	- GWTP max 1.5.3
    	- GWT D3JS max 1.2.0 ?
- Possible **workarounds** to use Java 8 libraries
    - Use JDK 8 and retrolambda: 
    	- https://github.com/orfjackal/retrolambda
    - Split your code into 2 projects/modules, compiled in 2 phases
    	- https://discuss.gradle.org/t/building-a-multi-module-gwt-project-with-gradle/3707
    	- https://blog.oio.de/2014/09/05/avoid-multi-module-builds-gradle/

#### [](#header-1)GAE Flexible (aka managed VM)
#### [](#header-3)Pros  
- Supports **Java 8**  
- **Faster?** 
	- I haven't really tested this but on the surface my login goes much faster on flexible  
- **New versions of libraries**    
    
#### [](#header-3)Cons 
- **No free quota**
	- To reduce costs make sure app.yaml specifies 1 CPU
- **No EU hosting [yet]**
- **No built-in task queuing**
  - https://groups.google.com/forum/#!topic/google-appengine/C9Sv4XkyHG4
  - https://groups.google.com/forum/?fromgroups#!searchin/objectify-appengine/flexible%7Csort:relevance/objectify-appengine/_8qQicEYg7E/0_msm1v3AAAJ
- Manually **manage static files** – to blob?
- **Objectify doesn't work** because it uses GAE standard API
  - Objectify developer puts a big maybe on migration
  - Workaround is to use Cloud Datastore REST api (https://github.com/GoogleCloudPlatform/google-cloud-java/tree/master/google-cloud-datastore)
    - via Catatumbo (Objectify doesn't work on GAE Flex [yet])

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)
