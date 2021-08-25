# Use CodeArtifact with Gradle<a name="maven-gradle"></a>

After you have the CodeArtifact auth token in an environment variable as described in [Pass an auth token using an environment variable](tokens-authentication.md#env-var), follow these instructions to consume Maven packages from, and publish new packages to, a CodeArtifact repository\.

**Topics**
+ [Fetch dependencies](#fetching-dependencies)
+ [Fetch plugins](#fetching-plugins)
+ [Publish artifacts](#publishing-artifacts)
+ [Run a Gradle build in IntelliJ IDEA](#gradle-intellij)

## Fetch dependencies<a name="fetching-dependencies"></a>

To fetch dependencies from CodeArtifact in a Gradle build, add a `maven` section to the `repositories` section in the project `build.gradle` file\.

```
maven {
         url 'https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/'
         credentials {
             username "aws"
             password System.env.CODEARTIFACT_AUTH_TOKEN
         }
}
```

The `url` in the sample above is your CodeArtifact repository's endpoint\. Gradle uses the endpoint to connect to your repository\. In the sample, `my_domain` is the name of your domain, `111122223333` is the ID of the owner of the domain, and `my_repo` is the name of your repository\. You can retrieve a repository's endpoint by using the `get-repository-endpoint` AWS CLI command\.

For example, with a repository named *my\_repo* inside a domain named *my\_domain*, the command is as follows:

```
aws codeartifact get-repository-endpoint --domain my_domain --domain-owner 111122223333 --repository my_repo --format maven
```

The `get-repository-endpoint` command will return the repository endpoint:

```
url 'https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/'
```

To use the CodeArtifact repository as the only source for your project dependencies, remove any other sections in `repositories` from `build.gradle`\. If you have more than one repository, Gradle searches each repository for dependencies in the order they are listed\.

After you configure the repository, you can add project dependencies to the `dependencies` section with standard Gradle syntax\.

```
dependencies {
    implementation 'com.google.guava:guava:27.1-jre'
    implementation 'commons-cli:commons-cli:1.4'
    testImplementation 'org.testng:testng:6.14.3'
}
```

## Fetch plugins<a name="fetching-plugins"></a>

By default Gradle will resolve plugins from the public [Gradle Plugin Portal](https://plugins.gradle.org/)\. To pull plugins from a CodeArtifact repository, add a `pluginManagement` block to your `settings.gradle` file\. The `pluginManagement` block must appear before any other statements in `settings.gradle`:

```
pluginManagement {
    repositories {
        maven {
            name 'my_repo'
            url 'https://my_domain-111122223333.codeartifact.region.amazonaws.com/maven/my_repo/'
            credentials {
                username 'aws'
                password System.env.CODEARTIFACT_AUTH_TOKEN
            }
        }
    }
}
```

This will ensure that Gradle resolves plugins from the specified repository\. The repository must have an upstream repository with an external connection to the Gradle Plugin Portal \(e\.g\. `gradle-plugins-store`\) so that commonly\-required Gradle plugins are available to the build\. For more information, see the [Gradle documentation](https://docs.gradle.org/current/userguide/plugins.html#sec:custom_plugin_repositories)\.

## Publish artifacts<a name="publishing-artifacts"></a>

This section describes how to publish a Java library built with Gradle to a CodeArtifact repository\.

First, add the `maven-publish` plugin to the `plugins` section of the project's `build.gradle` file\.

```
plugins {
    id 'java-library'
    id 'maven-publish'
}
```

Next, add a `publishing` section to the project `build.gradle` file\.

```
publishing {
    publications {
        mavenJava(MavenPublication) {
            groupId = 'group-id'
            artifactId = 'artifact-id'
            version = 'version'
            from components.java
        }
    }
    repositories {
        maven {
            url 'https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/'
            credentials {
                username "aws"
                password System.env.CODEARTIFACT_AUTH_TOKEN
            }
        }
    }
}
```

The `maven-publish` plugin generates a POM file based on the `groupId`, `artifactId`, and `version` specified in the `publishing` section\.

After these changes to `build.gradle` are complete, run the following command to build the project and upload it to the repository\.

```
./gradlew publish
```

Use `list-package-versions` to check that the package was successfully published\.

```
aws codeartifact list-package-versions --domain my_domain --domain-owner 111122223333 --repository my_repo --format maven\
  --namespace com.company.framework --package my-package-name
```

Sample output:

```
{
    "format": "maven",
    "namespace": "com.company.framework",
    "package": "example",
    "versions": [
        {
            "version": "1.0", 
            "revision": "REVISION-SAMPLE-1-C7F4S5E9B772FC",
            "status": "Published"
        }
    ]
}
```

For more information, see these topics on the Gradle website:
+  [Building Java Libraries](https://guides.gradle.org/building-java-libraries/) 
+  [Publishing a project as a module](https://docs.gradle.org/current/userguide/publishing_setup.html) 

## Run a Gradle build in IntelliJ IDEA<a name="gradle-intellij"></a>

You can run a Gradle build in IntelliJ IDEA that pulls dependencies from CodeArtifact\. To authenticate with CodeArtifact, you must provide Gradle with a CodeArtifact authorization token\. There are three methods to provide an auth token\.
+ Method 1: Storing the auth token in `gradle.properties`\. Use this method if you are able to overwrite or add to the contents of the `gradle.properties` file\.
+ Method 2: Storing the auth token in a separate file\. Use this method if you do not want to modify your `gradle.properties` file\.
+ Method 3: Generating a new auth token for each run by running `aws` as an inline script in `build.gradle`\. Use this method if you want the Gradle script to fetch a new token on each run\. The token won't be stored on the file system\.

------
#### [ Token stored in gradle\.properties ]

**Method 1: Storing the auth token in `gradle.properties`**
**Note**  
The example shows the `gradle.properties` file located in `GRADLE_USER_HOME`\.

1. Update your `build.gradle` file with the following snippet:

   ```
   repositories {
       maven {
                url 'https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/'
                credentials {
                    username "aws"
                    password "$codeartifactToken"
                }   
       }   
   }
   ```

1. To fetch plugins from CodeArtifact, add a `pluginManagement` block to your `settings.gradle` file\. The `pluginManagement` block must appear before any other statements in `settings.gradle`\.

   ```
   pluginManagement {
       repositories {
           maven {
               name 'my_repo'
               url 'https://my_domain-111122223333.codeartifact.region.amazonaws.com/maven/my_repo/'
               credentials {
                   username 'aws'
                   password "$codeartifactToken"
               }
           }
       }
   }
   ```

1. Fetch a CodeArtifact auth token:

   ```
   export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text --profile profile-name`
   ```

1. Write the auth token into the `gradle.properties` file:

   ```
   echo "codeartifactToken=$CODEARTIFACT_AUTH_TOKEN" > ~/.gradle/gradle.properties
   ```

------
#### [ Token stored in separate file ]

**Method 2: Storing the auth token in a separate file**

1. Update your `build.gradle` file with the following snippet:

   ```
   def props = new Properties()
   file("file").withInputStream { props.load(it) }
   
   repositories {
   
       maven {
                url 'https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/'
                credentials {
                    username "aws"
                    password props.getProperty("codeartifactToken")
                }
       }
   }
   ```

1. To fetch plugins from CodeArtifact, add a `pluginManagement` block to your `settings.gradle` file\. The `pluginManagement` block must appear before any other statements in `settings.gradle`\.

   ```
   pluginManagement {
       def props = new Properties()
       file("file").withInputStream { props.load(it) }
       repositories {
           maven {
               name 'my_repo'
               url 'https://my_domain-111122223333.codeartifact.region.amazonaws.com/maven/my_repo/'
               credentials {
                   username 'aws'
                   password props.getProperty("codeartifactToken")
               }
           }
       }
   }
   ```

1. Fetch a CodeArtifact auth token:

   ```
   export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text --profile profile-name`
   ```

1. Write the auth token into the file that was specified in your `build.gradle` file:

   ```
   echo "codeartifactToken=$CODEARTIFACT_AUTH_TOKEN" > file
   ```

------
#### [ Token generated for each run in build\.gradle ]

**Method 3: Generating a new auth token for each run by running `aws` as an inline script in `build.gradle`**

1. Update your `build.gradle` file with the following snippet:

   ```
   def codeartifactToken = "aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text --profile profile-name".execute().text
       repositories {
           maven {
               url 'https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/'
               credentials {
                   username "aws"
                   password codeartifactToken
               }
           }
       }
   ```

1. To fetch plugins from CodeArtifact, add a `pluginManagement` block to your `settings.gradle` file\. The `pluginManagement` block must appear before any other statements in `settings.gradle`\.

   ```
   pluginManagement {
       def codeartifactToken = "aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text --profile profile-name".execute().text
       repositories {
           maven {
               name 'my_repo'
               url 'https://my_domain-111122223333.codeartifact.region.amazonaws.com/maven/my_repo/'
               credentials {
                   username 'aws'
                   password codeartifactToken
               }
           }
       }
   }
   ```

------