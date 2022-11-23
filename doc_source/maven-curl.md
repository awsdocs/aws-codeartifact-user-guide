# Publishing with curl<a name="maven-curl"></a>

This section shows how to use the HTTP client `curl` to publish Maven artifacts to a CodeArtifact repository\. Publishing artifacts with `curl` can be useful if you do not have or want to install the Maven client in your environments\.

**Publish a Maven artifact with `curl`**

1. Fetch a CodeArtifact authorization token by following the steps in [Pass an auth token using an environment variable](tokens-authentication.md#env-var) and return to these steps\.

1. Use the following `curl` command to publish the JAR to a CodeArtifact repository:

   In each of the `curl` commands in this procedure, replace the following placeholders:
   + Replace *my\_domain* with your CodeArtifact domain name\.
   + Replace *111122223333* with the ID of the owner of your CodeArtifact domain\.
   + Replace *us\-west\-2* with the region in which your CodeArtifact domain is located\.
   + Replace *my\_repo* with your CodeArtifact repository name\.

   ```
   curl --request PUT https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/maven/my_repo/com/mycompany/app/my-app/1.0/my-app-1.0.jar \
        --user "aws:$CODEARTIFACT_AUTH_TOKEN" --header "Content-Type: application/octet-stream" \
        --data-binary @my-app-1.0.jar
   ```
**Important**  
You must prefix the value of the `--data-binary` parameter with a `@` character\. When putting the value in quotation marks, the `@` must be included inside the quotation marks\.

1. Use the following `curl` command to publish the POM to a CodeArtifact repository:

   ```
   curl --request PUT https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/maven/my_repo/com/mycompany/app/my-app/1.0/my-app-1.0.pom \
        --user "aws:$CODEARTIFACT_AUTH_TOKEN" --header "Content-Type: application/octet-stream" \
        --data-binary @my-app-1.0.pom
   ```

1. At this point, the Maven artifact will be in your CodeArtifact repository with a status of `Unfinished`\. To be able to consume the package, it must be in the `Published` state\. You can move the package from `Unfinished` to `Published` by either uploading a `maven-metadata.xml` file to your package, or calling the [UpdatePackageVersionsStatus API](https://docs.aws.amazon.com/codeartifact/latest/APIReference/API_UpdatePackageVersionsStatus.html) to change the status\.

   1.  Option 1: Use the following `curl` command to add a `maven-metadata.xml` file to your package: 

      ```
      curl --request PUT https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/maven/my_repo/com/mycompany/app/my-app/maven-metadata.xml \
           --user "aws:$CODEARTIFACT_AUTH_TOKEN" --header "Content-Type: application/octet-stream" \
           --data-binary @maven-metadata.xml
      ```

      The following is an example of the contents of a `maven-metadata.xml` file:

      ```
      <metadata modelVersion="1.1.0">
          <groupId>com.mycompany.app</groupId>
          <artifactId>my-app</artifactId>
          <versioning>
              <latest>1.0</latest>
              <release>1.0</release>
              <versions>
                  <version>1.0</version>
              </versions>
              <lastUpdated>20200731090423</lastUpdated>
          </versioning>
      </metadata>
      ```

   1.  Option 2: Update the package status to `Published` with the `UpdatePackageVersionsStatus` API\. 

      ```
      aws codeartifact update-package-versions-status \
          --domain my_domain \
          --domain-owner 111122223333 \
          --repository my_repo \
          --format maven \
          --namespace com.mycompany.app \
          --package my-app \
          --versions 1.0 \
          --target-status Published
      ```

If you only have an artifact's JAR file, you can publish a consumable package version to a CodeArtifact repository using `mvn`\. This can be useful if you do not have access to the artifact's source code or POM\. See [Publish third\-party artifacts](maven-mvn.md#publishing-third-party-artifacts) for details\.