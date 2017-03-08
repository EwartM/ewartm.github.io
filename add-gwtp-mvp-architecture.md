# [](#header-1)Add GWTP Model View Presenter (MVP) architecture

> GWTP will help us with browser history management, lazy loading, code splitting and security while making the code more scalable  

> GWTP uses Guice / Gin for Dependency Injection which helps us reading, testing and reusing code  

1. Java 7
1. GWT2.8.0beta1
1. GAE 1.9.42
1. Gradle
1. IntelliJ IDEA Ultimate 2016.3
1. **GWTP 1.5.3 Model View Presenter architecture**
1. **Guice 3.0 Dependency Injection**

[Github version after completing this tutorial](https://github.com/EwartM/MyAppName/tree/58b5ff3e9d70180abe3a92b65f500482c2c0b581)

#### [](#header-2)Prerequisite

[Read the GWTP basics](https://dev.arcbees.com/gwtp/)  
[Download & install the GWTP IntelliJ plugin](https://plugins.jetbrains.com/plugin/7318-gwt-platform-gwtp-intellij-idea-plugin)  
After downloading go to IntelliJ IDEA > Settings > Plugins > Install from file

#### [](#header-2)Overview

At the end of this tutorial the project structure will look like this:
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-08 at 12.23.16 PM.png)

#### [](#header-2)'NameTokens' file (part 1)

Create a New > Package (folder) called 'place' under App.src.com.example.app.client  
Create a New > Java class called 'NameTokens' with the content below in the 'place' folder  
```java
package com.example.app.client.place;

public class NameTokens {
}
```

#### [](#header-2)'Application' presenter  
This presenter will be the base presenter inside which all other will display  

Create a new Presenter via New > 'create GWTP Presenter with View'  
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 8.21.34 PM.png)

Name the presenter Application and create it in the client.ui package
Uncheck 'Place' and check 'UI Handlers'    
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 6.09.42 PM.png)  

Edit **ApplicationView**
```java
package com.example.app.client.ui;

import com.google.gwt.uibinder.client.UiBinder;
import com.google.gwt.uibinder.client.UiField;
import com.google.gwt.user.client.ui.SimplePanel;
import com.google.gwt.user.client.ui.Widget;
import com.gwtplatform.mvp.client.ViewWithUiHandlers;
import com.google.inject.Inject;

public class ApplicationView extends ViewWithUiHandlers<ApplicationUiHandlers> implements ApplicationPresenter.MyView {
    interface Binder extends UiBinder<Widget, ApplicationView> {
    }

    @UiField
    SimplePanel main;

    @Inject
    ApplicationView(Binder uiBinder) {
        initWidget(uiBinder.createAndBindUi(this));

        bindSlot(ApplicationPresenter.SLOT_APPLICATION, main);
    }
}
```

Save all files    

#### [](#header-2)'Home' presenter  
The 'Home' presenter will be the default page   

Add a new Presenter via New > 'create GWTP Presenter with View'    

Name the presenter Home and create it in the **..client.ui.home** package  
Check 'Place' and fill 'HOME'  
Select 'Slot' and browse to the Application presenter's SLOT_APPLICATION  
check 'UI Handlers'   
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 6.16.37 PM.png)

We want to move the 'Click Me' button and label out of the generated 'app' file and into our new GWTP presenter. You can delete com.example.app.client.app as we won't be using it anymore.  

Edit **HomeView.ui.xml** to generate the new 'Click Me' button and label.
```XML
<!DOCTYPE ui:UiBinder SYSTEM "http://dl.google.com/gwt/DTD/xhtml.ent">
<ui:UiBinder xmlns:ui="urn:ui:com.google.gwt.uibinder"
             xmlns:g="urn:import:com.google.gwt.user.client.ui">
    <g:HTMLPanel ui:field="main">
        <g:Button ui:field="btn_clickMe" text="Click Me"/>
        <g:Label ui:field="lbl_result"/>
    </g:HTMLPanel>
</ui:UiBinder>
```

Edit **HomeView** to implement the server call. The server call currently uses GWT-RPC, something we'll change later. For now we'll just add a click handler and the generated GWT-RPC call.
```java
package com.example.app.client.ui.home;

import com.example.app.client.appService;
import com.google.gwt.event.dom.client.ClickEvent;
import com.google.gwt.event.dom.client.ClickHandler;
import com.google.gwt.uibinder.client.UiBinder;
import com.google.gwt.uibinder.client.UiField;
import com.google.gwt.user.client.rpc.AsyncCallback;
import com.google.gwt.user.client.ui.*;
import com.google.inject.Inject;
import com.gwtplatform.mvp.client.ViewWithUiHandlers;

public class HomeView extends ViewWithUiHandlers<HomeUiHandlers> implements HomePresenter.MyView {
    interface Binder extends UiBinder<Widget, HomeView> {
    }

    @UiField
    HTMLPanel main;
    @UiField
    Button btn_clickMe;
    @UiField
    Label lbl_result;

    @Inject
    HomeView(Binder uiBinder) {
        initWidget(uiBinder.createAndBindUi(this));

        btn_clickMe.addClickHandler(new ClickHandler() {
            public void onClick(ClickEvent event) {
                if (lbl_result.getText().equals("")) {
                    appService.App.getInstance().getMessage("Hello, World!", new MyAsyncCallback(lbl_result));
                } else {
                    lbl_result.setText("");
                }
            }
        });
    }

    private static class MyAsyncCallback implements AsyncCallback<String> {
        private Label label;

        public MyAsyncCallback(Label label) {
            this.label = label;
        }

        public void onSuccess(String result) {
            label.getElement().setInnerHTML(result);
        }

        public void onFailure(Throwable throwable) {
            label.setText("Failed to receive answer from server!");
        }
    }
}
```

Edit **HomePresenter**
```java
package com.example.app.client.ui.home;

import com.example.app.client.place.NameTokens;
import com.example.app.client.ui.ApplicationPresenter;
import com.google.inject.Inject;
import com.google.web.bindery.event.shared.EventBus;
import com.gwtplatform.mvp.client.HasUiHandlers;
import com.gwtplatform.mvp.client.Presenter;
import com.gwtplatform.mvp.client.View;
import com.gwtplatform.mvp.client.annotations.NameToken;
import com.gwtplatform.mvp.client.annotations.ProxyStandard;
import com.gwtplatform.mvp.client.presenter.slots.NestedSlot;
import com.gwtplatform.mvp.client.proxy.ProxyPlace;

public class HomePresenter extends Presenter<HomePresenter.MyView, HomePresenter.MyProxy> implements HomeUiHandlers {
    interface MyView extends View, HasUiHandlers<HomeUiHandlers> {
    }

    @NameToken(NameTokens.HOME)
    @ProxyStandard
    interface MyProxy extends ProxyPlace<HomePresenter> {
    }

    public static final NestedSlot SLOT_HOME = new NestedSlot();

    @Inject
    HomePresenter(
            EventBus eventBus,
            MyView view,
            MyProxy proxy) {
        super(eventBus, view, proxy, ApplicationPresenter.SLOT_APPLICATION);

        getView().setUiHandlers(this);
    }

}
```

The other generated files can be left as generated by the GWTP plugin.


#### [](#header-2)Edit the 'NameTokens' file (part 2)

Edit the **place/NameTokens** file to look like the following:
```java
package com.example.app.client.place;

public class NameTokens {
    public static final String HOME = "/";

    public static String getHOME() {
        return HOME;
    }
}
```

#### [](#header-2)GIN 'ClientModule' file 

Create a New > Package (folder) called 'gin' under App.src.com.example.app.client
Create a New > Java class called 'ClientModule' with the content below in the 'gin' folder
```java
package com.example.app.client.gin;

import com.example.app.client.place.NameTokens;
import com.example.app.client.ui.home.HomeModule;
import com.example.app.client.ui.login.LoginModule;
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
                .errorPlace(NameTokens.HOME) //TODO
                .unauthorizedPlace(NameTokens.LOGIN)
                .build());

        install(new ApplicationModule());
    }
}
```

NOTE: GWTP will automatically add the 'install(new WhateverModule());' line to the parent presenter (the one whose slot will load WhateverModule) so it shouldn't be necessary to bind it in the 'ClientModule' class.


#### [](#header-2)'MyEntryPoint' file 
 
Create a New > Java class called 'MyEntryPoint' with the content below in the 'client' folder  
```java
package com.example.app.client;

import com.google.gwt.core.client.EntryPoint;
import com.google.gwt.core.client.GWT;
import com.gwtplatform.mvp.client.ApplicationController;

public class MyEntryPoint implements EntryPoint {
    private static final ApplicationController CONTROLLER =
            GWT.create(ApplicationController.class);

    @Override
    public void onModuleLoad() {

        CONTROLLER.init();
    }
}
```


#### [](#header-2)Inherit GWTP in the app.gwt.xml (module XML) file

Add the GWTP inherit in **app.gwt.xml** found under App.src.com.example.app 
```XML
<!DOCTYPE module PUBLIC "-//Google Inc.//DTD Google Web Toolkit 2.8.0//EN"
        "http://gwtproject.org/doctype/2.8.0/gwt-module.dtd">
<module rename-to="app">

    <!-- Inherit the core Web Toolkit stuff.            -->
    <inherits name='com.google.gwt.user.User'/>

    <!-- Specify the app entry point.                   -->
    <inherits name="com.gwtplatform.mvp.MvpWithEntryPoint"/>
    <set-configuration-property name="gwtp.bootstrapper"
                                value="com.example.app.client.ClientBootstrapper"/>
    <!-- GIN module.                   					-->
    <extend-configuration-property name="gin.ginjector.modules"
                                   value="com.example.app.client.gin.ClientModule"/>
    <!-- Translatable code.                   			-->
    <source path="client"/>
    <source path="shared"/>

    <!-- Specify the app servlets.                   	-->
    <servlet path='/appService' class='com.example.app.server.appServiceImpl'/>

</module>
```

#### [](#header-2)'app.html' file

Edit **app.html** found under App.web  
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

This version of the code should produce  
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-07 at 10.56.37 PM.png)


[Previous - basic project](intelliJ-basic-project)    
[Next - adding Bootstrapper & Login presenter](bootstrapper-and-login)
