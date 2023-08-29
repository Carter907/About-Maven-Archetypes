# About-Maven-Archetypes

### What is the point?

One of the major goals in programming is to harbour re-usability and scalability.
One way in which we can reuse code is by creating a templating system that allows 
you to quickly get up and running with predefined code that you're confident in.
Maven archetypes one example of this system in action. 

### How do I create my own?

You can create your own Maven Archetype by using a Maven Archetype! Using the predefined
org.apache.maven.archetypes:maven-archetype-archetype. Make sure you choose the archetype from 
Maven Central, as it has the most up-to-date version. You can also safely delete the test directory of the root folder
as we this is simply a demonstration. Once you've done that, you can expect the following project structure. 

```text
my-archetype/
|-- src/
|   |-- main/
|   |   |-- resources/
|   |   |   |-- archetype-resources/
|   |   |   |   |-- src/
|   |   |   |       |-- main/
|   |   |   |           |-- java/
|   |   |   |           |   |-- App.java
|   |   |   |           |-- test/
|   |   |   |           |   |-- TestApp.java
|   |   |   |-- META-INF/
|   |   |       |-- maven/
|   |   |           |-- archetype-meta.xml  
|   |   |                   
|   |   |-- pom.xml
|-- pom.xml
```

### archetype-meta.xml

`archetype.meta.xml` provides metadata for your archetype such as definitions for folder locations
and how they should behave. More specifically, this is where you will define the expected layout
of your source files, tests, and other properties that you will use in your project.
Those files are created directly by you in the archetype-resources folder. 

```xml
<archetype-descriptor xmlns="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-descriptor/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-descriptor/1.0.0 http://maven.apache.org/xsd/archetype-descriptor-1.0.0.xsd"  
  name="${artifactId}">

  <fileSets> <!-- fileSets are used to denote some arbitrary group of folders in your project. -->
      <!-- per maven project specification, you should have a directory for your java source files -->
    <fileSet filtered="true" packaged="true">
      <directory>src/main/java</directory>                                                                                                                           
    </fileSet>                                                                                       
      <!-- and a directory for your test files -->
    <fileSet filtered="true" packaged="true">                                                                                                                        
      <directory>src/test/java</directory>                                                                                                                           
    </fileSet>                                                                                                                                                       
  </fileSets>
</archetype-descriptor>
```

### Filtered & Packaged attributes

`filtered` - You will be using property binding in the java source files to
bootstrap any required properties.

`packaged` - the fileset will be prepended to the package or group-id of the newly created
project.

### Required Properties

Required properties allow you to specify parameters that will
be queried to the user when building your archetype with the help of `mvn achetype:generate`. These properties
can have a `defaultValue` which will be used if the user does not input any value. these properties are interpolated into
the scaffold created in `archetype-resources`. You can learn more about this templating is done by checking out the [Apache Velocity Project](https://velocity.apache.org/)
```xml
<requiredProperties>
    <requiredProperty key="about">
        <defaultValue>no about specified</defaultValue>
    </requiredProperty>
    <requiredProperty key="color"/>
    <requiredProperty key="due-date">
        <defaultValue>10 days from today</defaultValue>
    </requiredProperty> 
</requiredProperties>

```
here we create a few required properties. Only color will be required by mvn command but all will be queried if 
interactive mode is remained on.


### Defining Properties
```java
package $package;

/**
 *
 *  ${about}
 *  due-date: ${due-date}
 */
public class App
{
    public static void main( String[] args )
    {
        System.out.println("Your favorite color is ${color}");
    }
}

```

### Installing

Installing is a matter of calling `mvn clean install` to install the archetype
to our local repository, `.m2/repository`, which can be found as a hidden folder under your user directory.


### Generating a project using the Archetype & Final Result

Now it's time to finally generate the template and retrieve it from our
local repository which will be referenced automatically when we call `mvn achetype:generate`

```
mvn archetype:generate
    -DgroupId=com.example \
    -DartifactId=my-archetype-created \
    -DarchetypeArtifactId=my-new-maven-archetype \
    -DarchetypeGroupId=org.example \
    -DarchetypeVersion=1.0-SNAPSHOT \
    -DinteractiveMode=false \
    -Dcolor=red \
    -Ddue-date=09/05/2023 \
    -Dabout='This project was created using Maven Archetypes, and this text is from the command line'


```

This command will generate the project in the current directory
using your artifact id (`my-archetype-created` in the above example).
Now that we have are project set, we can view the templating taken place in `App.java`

```java
package com.example;

/**
 *
 *  This project was created using Maven Archetypes, and this text is from the command line
 *  due-date: 09/05/2023
 */
public class App
{
    public static void main( String[] args )
    {
        System.out.println("Your favorite color is red");
    }
}
```






