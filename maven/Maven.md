Is a Build automation tool (like Ant of MAKE) which creates deployable artifacts that has also:
- dependency management capabilities, pull dependencies from a centralized repository, it automatically detects transitive dependencies
- project management tool: include the information of the software (owners, version, name)
- Command line tool: it's a CLI tool that helps you build the app (it's also IDE integrated)
.it is managed by Apache.
Maven is build to work in different languages, but it has a lot of plugins making it perfect for the java ecosystem.

Maven is opinionated: it dictates a lot over the project structure, it enables conformity of the different projects that are built with maven. Convention over configuration.

Maven components:
**Project Object Model**: an XML file describing the maven configuration of a project.
**Repositories**: datastore of artifacts and dependencies, they can be local (your .m2 directory) or **remote**. the local repository is working as cache for the remote one
**Plugin**: is a collection of goals (goals are the action that we perform, for example compile-source)
Each plugin is attached to a **lifecycle** of the project (they are a set of phases). Executing a phase will execute all previous phases (**install** is a phase, and it will execute also the **compile** and **test** phase that are before in the lifecycle, **package** is another phase). When you don't specify the plugin you are using the default aven plugin (for install, test or package)

When starting a build, maven is reading through the pom to get project info, dependencies and the plugins needed in the build.
For the dependencies resolution, maven uses a DEPENDENCY MANAGER that search for them.
(it also download the plugins in the same way as dependencies)

## POM
inside the POM, under the root *project* tag, the *modelVersion* property is setting the version of language used in the POM itself, it's one of the mandatory tags, together with *artifactId*, *groupId*, *version*.
An important facultative element: *packaging* jar/war/ear, *name* informal name, *description*
Also licenses and developers:

```xml
<licenses>
	<license>
			 <name>Apache License</name>
			 <comments></comments>
	 </license>
 <licenses>
<developers>
	<developer>
		<name>...</name>
		<email>...</email>
	</developer>
</developers>
```
those informations can be used to generate a website page with the phase: **mvn site**
(you will find the index.html in the target/site)

### STANDARD DIRECTORY LAYOUT
the most common sources are:
src/main/java
src/main/resources
src/main/webapp (contain the css js and jsp static files)
src/test/java
src/test/resources
README.md
LICENCE.txt

How to tell maven to use non-standard directories? (it's better to stick to the standard)
```xml
<build>
	<sourceDirectory>src/nonstandard/java</sourceDirectory>   //java sources
	<testSourceDirectory>src/nonstandard/java</testSourceDirectory>  // test sources
	<directory>myCustomTargetDir</directory>      //output target directory
</build>
```

### INHERITANCE
POM's can inherit from one another, any POM is actually inheriting from the maven superpom that is included in the maven program itself, you can see the effective pom for your project by running
```
mvn help:effective-pom
```

in order to inherit from another pom we need to refer to it:
```xml
<parent>
	<artifactId>...</artifactId>
	<version></version>
	<groupId></groupId>	
</parent>
```

### PROFILES
with profiles you can customize your information within the POM, for example having a different build output directory for production environment. Each profile needs to have an ID
```xml
<profiles>
	<profile>
		<id>production</id>
		<build>
			<directory>myCustomProdTargetDirectory</directory>
		</build>
	</profile>
</profiles>
```
then you can build using that profile with this command:
```
mvn install -Pproduction
```

You can also use an activation strategy to automatically activate a profile without any -P inputs.
for example here we are activating with a environment property
```xml
<profiles>
	<profile>
		<id>production</id>
		<activation>
			<property>
				<name>MY_ENV_PROPERTY_NAME</name>
				<value>PRODUCTION</value>
			</property>
		</activation>
		<build>
			<directory>myCustomProdTargetDirectory</directory>
		</build>
	</profile>
</profiles>
```
or you can activate the profile by default:
```xml
<profiles>
	<profile>
		<id>production</id>
		<activation>
			<activeByDefault>true</activeByDefault>
		</activation>
	</profile>
</profiles>
```

you can automatically intiialize the project using this command and following the prompt required inputs:
```bash
mvn archetype:generate
```
## Dependency Management
 The maven central repository is the default repository from which maven is downloading the dependencies.  It is defined in the superpom
 You have a dependency plugin in maven that you can use to see the dependency tree:
 ```bash
 mvn dependency:tree
```
 locally you have a repository in your machine (under m2 folder) where the dependency that you have pulled are stored.
 
When conflict arises maven chooses the dependency closest to the root level, if more at the same level are present maven chooses the first one declared (in order of reading the POM). If we want to override a transitive dependency's version, we can include it in our POM with the desired version (in this way it will be at the root level of the tree).

The *dependencyManagement* section contains a set of dependencies used to specify the version that maven should use for each of them. It's useful in multi-modules projects, you define the version in the parent POM in the *dependencyManagement* section and then you can use the same dependency in the children's POM without the need to specify the version.
NB: you will need to add the dependency somewhere outside the *dependencyManagement* because adding it there is not telling maven to pull it.

### BOM
The bill of material is a special POM file used to group dependencies and their version, it has a 'pom' packaging and a *dependencyManagement* section that lists the dependencies needed

this BOM can be used as:
- parent pom: after having declared it as a parent, in your children modules you can refer to the dependencies needed without the need to specifiy the version
- a dependency: you can add the bom dependency in the dependencyManagement section of your project (specify the <type>pom</type>), then you can add the dependencies without need to specify the version

### PROFILES

You can create a profile with a personalized repository where it pulls dependencies from.
```xml
<profile>
	<id>spring-profile</id>
		<repositories>
			<repository>
				<id>spring-repo</id>
				<url>...</url>
			</repository>
		<repositories>
</profile>

and then
<activeProfiles>
<profile>spring-profile</profile>
</activeProfiles>
```
this is useful when the artifact is proprietary, or it's a snapshot version that is not accessible in the maven repository yet.

### Dependency Scope
- default scope is **compile** (the dep is available during build, test and run)
- the **test** scope: it is used only when running test (not used at compile or runtime for the app)
- scope **provided** : you expect the dependency to be provided by the JDK or the container, this means that the dependency is available during compiling, testing and developing but when you deploy the app you don't include it inside (example are the libs that are included in wildfly like jakarta.ServletAPI)
- scope **runtime**: when a dependency is needed for test and running but not for compilation (like the JDBC driver, during compilation you only need the JDBC interface)
- scope **system**: target a dependency as provided by the local system, it's placing the dependency in your filesystem (do not use, otherwise it will work only in your machine).

When you have different versions of the same dep included as transitive dependencies, maven will place only the most up to date version of the dep in the classpath by default,
you can explicitly exclude useless duplicate of dependency:
```xml
<dependency>
	<groupId>...</groupId>
	<artifactId>...</artifactId>
	<version>...</version>
	<exclusions>
		<exclusion>
			<groupId>...</groupId>
			<artifactId>...</artifactId>
		</exclusion>
	</exclusions>
```

## Plugin and lifecycle
maven has 3 built in plugin: default, site, help
clean, install, package are phases, they all run also their preceding phases, each phase is then splitted into single goals, each goal is executed by a specific plugin.

the default lifecycle contains 22 maven phases, but you can execute only a part of those phases:
- compile: is compiling the java source code and generate the .class files in the target
- test-compile: is compiling the java tests
- test: execute the tests with the surefire plugin
- package: take the compiled code and build the artifact (jar, ear, war)
- install: install the package into the local repository
- deploy: copies the package to the remote repository

you can run a mvn command with a non default plugin:
```
mvn compiler:compile
```
Maven under the hood is using a pluginRepository (in the same way as the dependency repository),
in the superpom there are some default plugins declared in a configured pluginRepository

How to customize a plugin property:
```xml
<build>
	<pluginManagement>
		<plugins>
			<plugin>
				<groupId>...</groupId>
				<artifactId>maven-compile-plugin</artifactId>
				<version>...</version>
				<configuration>
					<verbose>true</verbose>  // this is the verbose property of the plugin
				</configuration>
			</plugin>
		</plugins>
	</pluginManagement>
</build>
```

### Custom Plugin
How to develop your own plugin (you generally don't want to do it)
You need to create a java project that is the code behind the plugin.
the only class you need is a class extending **AbstractMojo**, this classs will override the **execute** method, in there you put the logic that needs to be executed when the plugin goal is executed.

You will also need to put some javadoc on the class, with the @goal goalname and @phase phasename specified

in the pom of the project defining the plugin you will need to customize the build:
```xml
<packaging>maven-plugin</packaging>

<build>
	<plugins>
		<plugin>
			<artifactId>maven-plugin-plugin</artifactId>
			<version>2.3</version>
			<configuration>
				<goalPrefix>myPluginName</goalPrefix>
			</configuration>
		</plugin>
	</plugins>
</build>

<dependencies> 
	<dependency> 
		<groupId>org.apache.maven</groupId> 
		<artifactId>maven-plugin-api</artifactId> 
		<version>...</version> 
	</dependency>
</dependencies>
```
in this way when using our created plugin we can reference to it with a name.
Then we can install the plugin in our local repo with mvn clean install.

The POM of the java project that is using our plugin
```XML
//inside the build and plugins
<plugin>
	<artifactId></artifactId>  //provide the coordinates of your developed plugin
	<groupId></groupId>
	<version></version>
	<executions>
		<execution>
			<id>namethegoal</id>
			<phase>compile</phase>  //binding the goal execution to a phase
			<goals>
				<goal>myPluginName</goal>
			</goals>
		</execution>
	</executions>
</plugin>
```

so that when we execute the compile phase of the project we will execute our custom plugin

### MAVEN CORE PLUGINS
Clean: it deletes the target folder (or any other ouputDirectory). clean is both the name pf the phase and the name of the plugin applying that phase (maven-clean-plugin).

jar: it allows to build a jar from a directory containing java code. (mvn jar:jar, jar plugin with jar goal, or mvn jar:test-jar). ITs paramter:
- finalName: name of the output jar
example:
```xml
<build>
	<pluginManagement>
		<plugins>
			<plugin>
				<artifactId>maven-jar-plugin</artifactId>
				<version>...</version>
				<configuration>
					<finalName>output</finalName>
				</configuration>
			</plugin>
		</plugins>
	</pluginManagement>
</build>
```


consider checking the online documentation of the plugin at https://maven.apache.org

javadoc plugin generates javadoc automatically from the code (mvn javadoc:javadoc): the output is the javadoc in the website format as a group of html files

the deploy plugin (used in the deploy phase) needs a remote repository to be set in the POM (under project):
```xml
<distributionManagement>
	<repository>
		<id>...</id>    // those three values are generally specified in your remote repository
		<name></name>   // like in artifactory if you set up a repository you will see these.
		<url>...</url>
	</repository>
</distributionManagement>
```

surefire: used by the test phase, you can skip the test execution with the mvn parameter: -Dmaven.test.skip=true
you can also configure the surefire plugin to pass even if tests fail:
inside plugin management and the surefire plugin
```xml
<configuration>
	<testFailureIgnore>true</testFailureIgnore>
</configuration>
```

## Archetypes
you can create a simple maven project using the archetype (like spring initializr)
```
mvn archetype:generate
```
you can also create your own archetype by setting the packaging property of your project to 'maven-archetype'

## Multi modules project
You can configure a packaging of type 'pom' meaning that that maven project doesn't contain any code but it's there just to configure maven, ticpically used as root pom:
```xml
<packaging>pom</packaging>
<modules>
	<module>subproject1</module>  //specify all the children modules with their POMs
</modules>
```
then when you say mvn compile, it will compile all the sub modules.

## DEPLOY TO TOMCAT
there is a maven-tomcat-plugin that deploys the war to apache tomcat

you need a user configured in apache (tomcat/conf/conf-users.xml)

in the settings.xml (in the installation folder of maven)
you need to add the tomcat credentials like this:
inside the servers tag
```xml
<server>
	<id>tomcat-server</id>
	<password>password</password>
	<username>username</username>
</server>
```

inside the POM of the project that you want to deploy:
```xml
<build>
	<finalName>myApp</finalName>
	<plugins>
		<plugin>
			<groupId>org.apache.tomcat.maven</groupId>
			<artifactId>tomcat7-maven-plugin</artifactId>
			<version>...</version>
			<configuration>
				<url>http://localhost:8080/manager/text</url>
				<server>tomcat-server</server>   //matching the name on the settings.xml
			</configuration>
		</plugin>
	</plugins>
</build>
```

then execute:
mvn tomcat7:deploy
mvn tomcat7:undeploy

## PROPERTIES
you can pull env properties, project properties or even java properties.
ex: ${project.artifactId} will use the artifactId of the project (from the root POM)
${java.version} will use the java version from the JDK target by the JAVA_HOME
${envPATH} will access the environment variable of the system called PATH


Plus you can add custom properties:
```xml
<properties>
	<myProperty>value</myProperty>
</properties>
```
