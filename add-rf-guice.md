# [](#header-1)Add Request Factory and Guice

> We'll replace vanilla GWT-RPC with Request Factory. RequestFactory keeps track of objects that have been modified and sends only changes to the server, which results in very lightweight network payloads.   

> Guice provides Dependency Injection on the server side  

1. Java 7
1. GWT2.8.0beta1
1. GAE 1.9.42
1. Gradle
1. IntelliJ IDEA Ultimate 2016.3
1. GWTP 1.5.3 Model View Presenter architecture
1. Guice 3.0 Dependency Injection
1. Request Factory

[Github version after completing this tutorial](https://github.com/EwartM/MyAppName/tree/dc0dd25f5b586477d6587c285381dc19d6f937bd)

#### [](#header-2)Prerequisite

[Read the Request Factory docs](http://www.gwtproject.org/doc/latest/DevGuideRequestFactory.html#using)

#### [](#header-2)Overview

At the end of this tutorial the project structure will look like this:
![]({{ site.baseurl }}/assets/images/Screen Shot 2017-03-08 at 6.52.48 PM.png)

#### [](#header-2)Inherit Request Factory

Inherit Request Factory in the app.gwt.mxl  

```XML
<!DOCTYPE module PUBLIC "-//Google Inc.//DTD Google Web Toolkit 2.8.0//EN"
        "http://gwtproject.org/doctype/2.8.0/gwt-module.dtd">
<module rename-to="app">

    <!-- Inherit the core Web Toolkit stuff.                  -->
    <inherits name='com.google.gwt.user.User'/>
    <inherits name='com.google.gwt.inject.Inject' />
    <inherits name="com.google.gwt.i18n.I18N"/>

    <!-- GWTP -->
    <inherits name='com.gwtplatform.mvp.Mvp'/>
    <entry-point class='com.example.app.client.MyEntryPoint'/>
    <!-- GIN -->
    <extend-configuration-property name="gin.ginjector.modules"
                                   value="com.example.app.client.gin.ClientModule"/>
    <!-- Specify the paths for translatable code -->
    <source path="client"/>
    <source path="shared"/>

    <!-- Request Factory -->
    <inherits name='com.google.web.bindery.requestfactory.RequestFactory' />

</module>
```

#### [](#header-2)'Users' Server POJO and Client Proxy  

With Request Factory we create a POJO on the server and corresponding Proxy on the client

Create a New > Java class called 'User' with the content below in the com.example.app.server.domain folder  
```java
package com.example.app.server.domain;

public class User extends DomainObject {

    //No primitives allowed
    private String firstName;
    private String lastName;
    private String email = null;
    private String cleartextPassword;
    private Boolean loggedIn = false;

    public String getFirstName() {
        return firstName;
    }
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
    public String getEmail() {
        return email;
    }
    public void setEmail(String email) {
        this.email = email;
    }
    public String getCleartextPassword() {
        return cleartextPassword;
    }
    public void setCleartextPassword(String cleartextPassword) {
        this.cleartextPassword = cleartextPassword;
    }
    public Boolean isLoggedIn() {
        return loggedIn;
    }
    public void setLoggedIn(Boolean loggedIn) {
        this.loggedIn = loggedIn;
    }
}
```

Create a New > Java class called 'UserProxy' with the content below in the com.example.app.client.proxy folder  
```java
package com.example.app.client.proxy;

import com.example.app.server.domain.User;
import com.google.web.bindery.requestfactory.shared.EntityProxy;
import com.google.web.bindery.requestfactory.shared.ProxyFor;

@ProxyFor(value = User.class)
public interface UserProxy extends EntityProxy {

    String getFirstName();
    void setFirstName(String firstName);

    String getLastName();
    void setLastName(String lastName);

    String getEmail();
    void setEmail(String email);

    String getCleartextPassword();
    void setCleartextPassword(String cleartextPassword);

    Boolean isLoggedIn();
}
```

#### [](#header-2)Base class for domain objects

Request Factory keeps track of object changes / versions. Every RF object needs to have an id & version so all our RF objects should extend the class below.  

Create a New > Java class called 'DomainObject' with the content below in the com.example.app.server.domain folder  
```java
package com.example.app.server.domain;

public class DomainObject {

    private Long id = null;

    private Integer version = 0;

    /**
     * Auto-increment version # whenever persisted
     */
    void onPersist()
    {
        this.version++;
    }

    public Long getId()
    {
        return id;
    }

    public void setId(Long id)
    {
        this.id = id;
    }

    public Integer getVersion()
    {
        return version;
    }

    public void setVersion(Integer version)
    {
        this.version = version;
    }

    // Equality based on ID field
    @Override
    public boolean equals(Object o)
    {
        if(o != null && (o instanceof DomainObject)) {
            return ((DomainObject)o).getId().compareTo(id) == 0;
        }
        return false;
    }

    // Required for checking equality
    @Override
    public int hashCode()
    {
        return ((Long)id).hashCode();
    }

}
```

#### [](#header-2)User service

We'll set up service for the client to interact with our User Pojo 

Create a New > Java class called 'UserService' with the content below in the com.example.app.server.service folder  
```java
package com.example.app.server.service;

import com.example.app.server.domain.User;

public class UserService {

    /**
     * PUBLIC
     * Check submitted credentials against DB
     */
    public User authenticate (String submittedEmail, String submittedPassword) {

        User user = null;

        String testEmail = "admin@example.com";
        String testPassword = "admin";

        if (testEmail != null) {
            if (submittedEmail.matches(testEmail) &&
                    submittedPassword.matches(testPassword)) {
                user = new User();
                user.setEmail(submittedEmail);
            }
        }

        return user;
    }
}
```

#### [](#header-2)Request Factory class

Create a New > Java class called 'MyRequestFactory' with the content below in the com.example.app.shared.service folder  

The service methods we created above must be added into our Request Factory class 

```java
package com.example.app.shared.service;

import com.example.app.client.proxy.UserProxy;
import com.example.app.server.locator.DaoServiceLocator;
import com.example.app.server.service.UserService;
import com.google.web.bindery.requestfactory.shared.Request;
import com.google.web.bindery.requestfactory.shared.RequestContext;
import com.google.web.bindery.requestfactory.shared.RequestFactory;
import com.google.web.bindery.requestfactory.shared.Service;

public interface MyRequestFactory extends RequestFactory {

    //USER
    UserRequest userRequest();

    @Service(value = UserService.class, locator = DaoServiceLocator.class)
    public interface UserRequest extends RequestContext {
        Request<UserProxy> authenticate(String submittedEmail, String submittedPassword);
    }

}
```



