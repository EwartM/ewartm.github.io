---
layout: default
---

# [](#header-1)Creating a new GWT & GAE project with IntelliJ IDEA

> Follow these steps in IntelliJ IDEA to create a GWT app running on GAE standard environment  
> We'll use Gradle to manage dependencies

1. Java 7
1. GWT2.8.0beta1
1. GAE 1.9.42
1. Gradle 3.1
1. IntelliJ IDEA Ultimate 2016.3

Create a new project.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.51.05%20PM.png

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

```xml
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

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.54.52%20PM.png)

Open the Gradle tab and select **build** to copy the libraries to the project.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.55.50%20PM.png)

Now let's add the application module.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.56.15%20PM.png)

Check Google App Engine and browse to your local GAE SDK. Note that GAE standard only supports up to GAE SDK 1.9.42

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.56.51%20PM.png)

Check Google Web Toolkit and browse to your local GWT SDK. Note that GAE standard only supports up to GWT2.8.0beta1 

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.57.20%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.57.45%20PM.png)

When done right-click the App module and select Module Settings.

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.58.23%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.58.49%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.59.06%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.59.21%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%201.59.35%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.00.00%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.00.33%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.01.01%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.01.25%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.01.35%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.02.46%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.03.01%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.03.23%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.04.15%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.04.36%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.04.49%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.05.07%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.05.19%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.05.55%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.06.13%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.07.02%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.06%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.21%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.29%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.08.50%20PM.png)

![]({{ site.baseurl }}/assets/images/Screen%20Shot%202017-03-01%20at%202.12.45%20PM.png)



Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](another-page).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# [](#header-1)Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## [](#header-2)Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### [](#header-3)Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### [](#header-4)Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### [](#header-5)Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### [](#header-6)Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
