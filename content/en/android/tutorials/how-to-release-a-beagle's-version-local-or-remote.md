---
title: How to release a Beagle's version local or remote
weight: 220
description: >-
 In this section, you will find how to release a version of Beagle locally or using a separate repository.
---

---

## Configuring Gradle

If you work on Android or Backend's applications, you must configure the **`gradle.properties`** files that can be found on the root folder. This configuration works on remote and local versions.

As soon as you open the file, the first step is to change the version name that will be released.  For that, you can change the **`VERSION_NAME`** variable value to the value you wish. 


{{% alert color="info" %}}
This variable helps on referencing the version name when you import Beagle. 
{{% /alert %}}

Besides, you have to add a variable called **`RELEASE_SIGNING_ENABLED`** and attribute it to the `false` value. That means you don't need to sign a version, once that this configuration allows a Beagle's version release in local way or to an own repository. 

After you finish these configurations, you must have on your gradel the following variables according to the name you chose to your version (on this example, **`beagle-test`** is used):


```kotlin
gradle.properties (root platform)

VERSION_NAME=beagle-test 
RELEASE_SIGNING_ENABLED=false
```


## Publishing in a remote repository

At the end of the **`gradle.properties`** configuration, you have to configure a file that is on Beagle's root folder, in this case, the  **`maven-publish.gradle`**. On this file, add a target that will define:

* In which repository the version will be released.
* The necessary credentials to access this repository. 


| Keys | Definition |
| :--- | :--- |
| **releaseRepositoryUrl** | Repository's URL where the version will be released.|
| **repositoryUsername** | User name. |
| **repositoryPassword** | Password of the declared user name. |

Check out below a publishing example:

```bash
maven-publish.gradle (root project)

apply plugin: "com.vanniktech.maven.publish"

def versionName = System.env.VERSION_DEPLOY != null ? System.env.VERSION_DEPLOY : VERSION_NAME ?: ""

version = versionName

mavenPublish {
nexus {
    		stagingProfile = "br.com.zup"
	}
	targets {
    		uploadArchives {    
        	releaseRepositoryUrl = "https://meu-repo.com.br"
        	repositoryUsername = "username"
        	repositoryPassword = "password"
    		}
	}
}
```


## Publishing in a local repository

At the end of the **`gradle.properties`** configuration, you must run some **gradle's commands**.

To do so, go to the root folder on the platform you wish to release the version (Android or Backend) and, in the terminal of your preference, run the following command:

**If you're using a Windows terminal:**

```bash
gradlew clean build publishToMavenLocal
```

**In case you're using another terminal:**

```bash
./gradlew clean build publishToMavenLocal
```

After you run the command, the version will be automatically released.  
