# Packaging Maven Projects

## Learning Goals

- Define Maven's package phase.
- Package a Java project into a non-executable JAR file. 
- Define snapshot versus release versioning.
- Add a JAR dependency to a project.
- Package a Java project as an executable JAR file.

## Introduction

Maven's package phase is responsible
for bundling all the files in the artifact, in this case a JAR-file.

A JAR (Java Archive) contains compiled code and other
data/resources, along with a manifest. 
The **manifest** is a special file that contains information
about the files packaged in a JAR file.
A JAR file can contain zero, one, or more main classes.
Each main class is a possible entry point for running an application.
A JAR file can have at most one entry point set in its manifest file
by assigning the `Main-Class` attribute, which specifies the class
to execute when the JAR is executed. In this case, the JAR file is an executable JAR.
A nonexecutable JAR doesn't have `Main-Class` defined in the manifest file.
While a nonexecutable JAR can't be executed, the classes contained
in the JAR can still be used in other applications.

## Packaging a project as a non-executable JAR

We will create a Maven project that contains a simple utility class `Calculator`.
Note the package is `org.jarexample` to differentiate it from the application package
`org.example` that will make use of the utility class.

![new jar project](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/newproject.png)

Create a new class `Calculator` in the `org.jarexample` directory:

```java
package org.jarexample;

public class Calculator {
    public static int add(int x, int y) {
        return x + y;
    }
    public static int subtract(int x, int y) {
        return x - y;
    }
    public static int multiply(int x, int y) {
        return x * y;
    }
    public static int divide(int x, int y) {
        return x / y;
    }
}
```

The `Calculator` class can be used in other projects by packaging the project into a jar file.

1. Right-click on the package phase in the Maven Tools Window:  
   ![maven package phase](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/mavenpackagephase.png)
2. Run the package phase:  
   ![run package phase](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/runpackagephase.png)


An alternative way to execute the package phase:
1. Click on the Execute Maven Goal icon:  
   ![execute goal icon](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/executegoal.png)
2. Select the Maven Package command:  
   ![execute package goal](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/executepackagegoal.png)


If we scroll through the output, we can see the life cycle processes that
precede package (i.e. compile, test, etc) were executed.  We also
see a  file "calculator-1.0-SNAPSHOT.jar" was created and placed in the target directory.

![build jar output](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/buildjar.png)

The JAR file name is determined by the artifactId and version specified
in POM.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.jarexample</groupId>
    <artifactId>calculator</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

</project>
```

## Snapshot vs Release Versioning

The coordinates in `POM.xml` contain a
version attribute that informs Maven about the project
repository type.  IntelliJ sets the version to "1.0.SNAPSHOT"
as a default when a new project is created.

```xml
<groupId>org.jarexample</groupId>
<artifactId>calculator</artifactId>
<version>1.0-SNAPSHOT</version>
```

We use a snapshot repository when a project is under development
and a release repository when the project is ready for production.

The snapshot is a repository used for incremental artifact versions.
Before releasing version 1.0.0 of
our project, we create its snapshot version 1.0.0-SNAPSHOT.

When a snapshot version is deployed, Maven will set the artifact
version to contain a timestamp value and build number
instead of the SNAPSHOT value.
Before downloading a snapshot artifact, Maven checks
if there's a newer version based on snapshot timestamp and build number.

A release repository contains a final version of artifacts
and represents content that should not be modified (at least not
for that release).

## Adding the JAR dependency in another project

We will create another new IntelliJ project, then add the JAR file calculator-1.0-SNAPSHOT.jar
as a dependency so the new project can use the `Calculator` class. 

Create a new Maven project with the groupId and artifactId as shown:

![test jar project](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/testproject.png)

The `main` method will use the `Calculator` class by calling the static `add` method:


```java
package org.example;

import org.jarexample.Calculator;

public class Main {
   public static void main(String[] args) {
      int result = Calculator.add(6,3);
      System.out.println(result);
   }
}
```

The code above won't compile because the project can't
find the `org.jarexample.Calculator` class.
We need to add the JAR file to the project.

1. Select File | Project Structure.
2. Select Module. Click "+". Select Jars or Dependencies:       
   ![add jar dependency](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/addjardependency.png)
3. Navigate to the location of the calculator project, open the target folder, select the JAR file:    
   ![jar file in file explorer](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/pickjarfile.png)
4. Click "OK" to add it to the project.

At this point the errors in the `main` method should resolve, and the program can execute
to print the output.

## Creating an executable JAR

We'll create another new project to step through the process of creating an executable JAR.

Create a new Maven project with the ArtifactId and GroupId as shown:

![new hello app](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/new-hello-app.png)

IntelliJ should have generated a default `Main` class:

```java
package org.jarexample;

public class Main {
    public static void main(String[] args) {
        System.out.println("Hello world!");
    }
}
```

By default, a JAR file does not know which class to execute
since there could be multiple classes containing a 'main' method.
We need to tell Maven to set the main class in the manifest to `org.jarexample.Main`. 
This can be specified in POM.xml or through the IntelliJ UI.
We will step through the process using the IntelliJ UI.

1. Select File | Project Settings from the main menu. 

2. Select Artifacts, then click the plus sign.   
   ![artifact plus](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/artifactplussign.png) 

3. Select "jar", then "from modules with dependencies".   
   ![add jar from modules](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/addjarmodules.png) 

4. Click the folder to select the `Main` class.  Press OK.     
   ![select main class for jar](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/pickmainclass.png) 

5. Press OK again to accept the settings.  
   ![confirm ok](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/okagain.png)  

6. Select Build | Build Artifacts from the main menu.    
   ![build artifacts](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/buildartifacts.png)  

7. Select the hello-app.jar to build.  
   ![build hello app](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/buildhelloapp.png)  

8. Expand the "out" folder in the Project view and confirm the new artifact hello-app.jar was generated.     
   ![out folder contains helloapp.jar](https://curriculum-content.s3.amazonaws.com/6002/packaging-maven-projects/helloappjar.png)


The hello-app.jar file is an executable JAR file.  We can right-click on the jar file and run it.
We can also navigate to the folder in the file explorer and run the jar file in the command line:

```code
java -jar hello-app.jar
```

The program prints:

```text
Hello world!
```

## Conclusion

We've seen how to use the IntelliJ UI to package Maven projects into executable and non-executable JAR files.

