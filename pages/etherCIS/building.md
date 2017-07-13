---
title: Building EtherCIS
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: building.html
folder: etherCIS
filename: building.md
---

## How To Build It?

### Maven

You need to compile each module as indicated in their respective README. A global setting for the assembly of uber jars should be done in your settings.xml. An example is given at ethercis/examples/

The sample launch script (ecis-server) assumes some jar assemblies to simplify the classpath. At the moment, there is no assembly provided in the pom's.

Locally, you should install the 'exotic' libraries required by Maven. These jars are located in directory 'libraries'. Local installation can be achieved with the following command for example:

	  mvn org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file  
	      -Dfile=/Development/Dropbox/eCIS_Development/eCIS-LIB/compositionTemplate.jar 
	      -DgroupId=org.openehr 
	      -DartifactId=org.openehr.openEHR.v1 
	      -Dversion=1.0.0 
	      -Dpackaging=jar 
	      -DlocalRepositoryPath=/Development/Dropbox/eCIS_Development/eCIS-LIB/local-maven-repo

### Gradle

[TO BE CONTINUED]
      
## How To Run It?

Script ecis-server should be adapted to get the right classpath, path to required configuration, network parameters etc.
Ditto for all configuration files.
The scripts and configuration samples are in directory examples

Script ecis-server uses uber jars to keep the modularity of the platform as well as to ease the production of patches. The jars are posted at libraries until a better file repository is identified.

[TO BE CONTINUED]