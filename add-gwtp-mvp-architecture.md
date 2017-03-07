# [](#header-1)Add GWTP Model View Presenter (MVP) architecture

> We'll use GWTP to implement a Model View Presenter architecture  
> GWTP will help us with browser history management, lazy loading, code splitting and security while making the code more scalable  
> GWTP uses Guice / Gin for Dependency Injection which helps us with reading, testing and reusing code  

1. Java 7
1. GWT2.8.0beta1
1. GAE 1.9.42
1. Gradle
1. IntelliJ IDEA Ultimate 2016.3
1. **GWTP 1.5.3** Model View Presenter architecture
1. **Guice 3.0** Dependency Injection

#### [](#header-2)Prerequisite

[Read the GWTP basics](https://dev.arcbees.com/gwtp/)  
[Download & install the GWTP IntelliJ plugin](https://plugins.jetbrains.com/plugin/7318-gwt-platform-gwtp-intellij-idea-plugin)  
IntelliJ IDEA > Settings > Plugins > Install from file

#### [](#header-2)Create the project and parent module

![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 7.41.08 PM.png)

Create a New > Package (folder) called 'place' under App.src.com.example.app.client  
Create a New > Java class called 'NameTokens' with the content below in the 'place' folder  
```java
package com.example.app.client.place;

public class NameTokens {
}
```

Create a New > Package (folder) called 'gin' under App.src.com.example.app.client
Create a New > Java class called 'ClientModule' with the content below in the 'gin' folder
```java
package com.example.app.client.gin;

import com.example.app.client.place.NameTokens;
import com.gwtplatform.mvp.client.gin.AbstractPresenterModule;
import com.gwtplatform.mvp.client.gin.DefaultModule;
import com.gwtplatform.mvp.shared.proxy.RouteTokenFormatter;
import com.example.app.client.ui.ApplicationModule;


public class ClientModule extends AbstractPresenterModule {
    @Override
    protected void configure() {
        install(new DefaultModule.Builder()
                .tokenFormatter(RouteTokenFormatter.class)
                .defaultPlace(NameTokens.HOME)
                .errorPlace(NameTokens.HOME)
                .unauthorizedPlace(NameTokens.HOME)
                .build());

        install(new ApplicationModule());
    }
}
```

Add a new Presenter via New > 'create GWTP Presenter with View'  
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 8.21.34 PM.png)

Call the new nested presenter called Application under ..client.ui  
Uncheck 'Place' and check 'UI Handlers'  
This presenter will be the base presenter inside which all other will display  
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 6.09.42 PM.png)

Edit 'app.html' under App.web  
```HTML
<html>
<head>

    <!--                                           -->
    <!-- Any title is fine                         -->
    <!--                                           -->
    <title>MyAppName</title>

    <link type="text/css" rel="stylesheet" href="app.css">

    <!--                                            -->
    <!-- This script is required bootstrap stuff.   -->
    <!--                                            -->
    <script type="text/javascript" language="javascript" src="app/app.nocache.js"></script>
</head>

<!--                                           -->
<!-- The body can have arbitrary html, or      -->
<!-- you can leave the body empty if you want  -->
<!-- to create a completely dynamic ui         -->
<!--                                           -->
<body>

<noscript>
    <p>
        Your web browser must have JavaScript enabled in order for this application to display correctly.
    </p>
</noscript>

</body>
</html>
```
