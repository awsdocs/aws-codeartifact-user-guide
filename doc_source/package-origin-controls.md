# Editing package origin controls<a name="package-origin-controls"></a>

In AWS CodeArtifact, package versions can be added to a repository by directly publishing them, pulling them down from an upstream repository, or ingesting them from an external, public repository\. Allowing package versions of a package to be added both by direct publishing and ingesting from public repositories makes you vulnerable to a dependency substitution attack\. For more information, see [Dependency substitution attacks](dependency-substitution-attacks.md)\. To protect yourself against a dependency substitution attack, you can configure package origin controls on a package in a repository to limit how versions of that package can be added to the repository\.

Configuring package origin controls should be considered by any team that wants to allow new versions of different packages to come from both internal sources, such as direct publishing, and external sources, such as public repositories\. By default, package origin controls will be configured based on how the first version of a package is added to the repository\. For information about the package origin control settings and their default values, see [Package origin control settings](#package-origin-control-settings)\.

## Common package access control scenarios<a name="package-origin-control-scenarios"></a>

This section includes some common scenarios when a package version is added to a CodeArtifact repository\. Package origin control settings will be set for new packages depending on how the first package version is added\.

In the following scenarios, an *internal package* is a package that is published directly from a package manager to your repository, such as a package that you or your team authors and maintains\. An *external package* is a package that exists in a public repository that can be ingested into your repository with an external connection\.

**An external package version is published for an existing internal package**

In this scenario, consider an internal package, *packageA*\. Your team publishes the first package version for *packageA* to a CodeArtifact repository\. Because this is the first package version for that package, the package origin control settings are automatically set to **Publish: Allow** and **Upstream: Block**\. After the package exists in your repository, a package with the same name is published to a public repository that is connected to your CodeArtifact repository\. This could be an attempted dependency substitution attack against the internal package, or it could just be a coincidence\. Regardless, package origin controls are configured to block the ingestion of the new external version to protect themselves against a potential attack\.

In the following image, *repoA* is your CodeArtifact repository with an external connection to a public repository\. Your repository contains versions 1\.1 and 2\.1 of *packageA*, but version 3\.0 is published to the public repository\. Normally, *repoA* would ingest version 3\.0 after the package was requested by a package manager\. Because package ingestion is set to **Block**, version 3\.0 is not ingested into your CodeArtifact repository and is not available to package managers connected to it\.

![\[Simple graphic showing a new external package version being blocked from a public repository.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple graphic showing a new external package version being blocked from a public repository.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple graphic showing a new external package version being blocked from a public repository.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

**An internal package version is published for an existing external package**

In this scenario, a package, *packageB* exists externally in a public repository that you have connected to your repository\. When a package manager connected to your repository requests *packageB*, the package version is ingested into your repository from the public repository\. Because this is the first package version of *packageB* added to your repository, the package origin settings are configured to **Publish: BLOCK** and **Upstream: ALLOW**\. Later, you try to publish a version with the same package name to the repository\. Either you are not aware of the public package and trying to publish an unrelated package under the same name, or you are trying to publish a patched version, or you are trying to directly publish the exact package version that already exists externally\. CodeArtifact will reject the version you are trying to publish, but allow you to explicitly override the rejection and publish the version if necessary\.

In the following image, *repoA* is your CodeArtifact repository with an external connection to a public repository\. Your repository contains version 3\.0 that it ingested from the public repository\. You want to publish version 1\.1 to your repository\. Normally, you could publish version 1\.2 to *repoA*, but because publishing is set to **Block**, version 1\.2 cannot be published\.

![\[Simple graphic showing a new external package version being blocked from a public repository.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple graphic showing a new external package version being blocked from a public repository.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple graphic showing a new external package version being blocked from a public repository.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

**Publishing a patched package version of an existing external package**

In this scenario, a package, *packageB* exists externally in a public repository that you have connected to your repository\. When a package manager connected to your repository requests *packageB*, the package version is ingested into your repository from the public repository\. Because this is the first package version of *packageB* added to your repository, the package origin settings are configured to **Publish: BLOCK** and **Upstream: ALLOW**\. Your team decides that it needs to publish patched package versions of this package to the repository\. To be able to publish package versions directly, your team changes the package origin control settings to **Publish: ALLOW** and **Upstream: BLOCK**\. Versions of this package can now be published directly to your repository and ingested from public repositories\. After your team publishes the patched package versions, your team reverts the package origin settings to **Publish: BLOCK** and **Upstream: ALLOW**\.

## Package origin control settings<a name="package-origin-control-settings"></a>

With package origin controls, you can configure how package versions can be added to a repository\. The following lists include the available package origin control settings and values\.

**Publish**

This setting configures whether package versions can be published directly to the repository using package managers or similar tools\.
+ **ALLOW**: Package versions can be published directly\.
+ **BLOCK**: Package versions cannot be published directly\.

**Upstream**

This setting configures whether package versions can be ingested from external, public repositories, or retained from upstream repositories when requested by a package manager\.
+ **ALLOW**: Any package version can be retained from other CodeArtifact repositories configured as upstream repositories or ingested from a public source with an external connection\.
+ **BLOCK**: Package versions cannot be retained from other CodeArtifact repositories configured as upstream repositories or ingested from a public source with an external connection\.

### Default package origin control settings<a name="default-package-origin-control-settings"></a>

The default package origin controls for a package will be based on how the first version of that package is added to the repository\.

**Note**  
Packages that existed in CodeArtifact repositories prior to around May 2022 will have a default package origin controls of **Publish: ALLOW** and **Upstream: ALLOW**\. Package origin controls must be set manually for such packages\. The current default values have been set on new packages since that time, and started being enforced when the feature launched on July 14, 2022\. For more information about setting package origin controls, see [Editing package origin controls](#edit-package-origin-controls)\.
+ If the first package version is published direcly by a package manager, the settings will be **Publish: ALLOW** and **Upstream: BLOCK**\.
+ If the first package version is ingested from a public source, the settings will be **Publish: BLOCK** and **Upstream: ALLOW**\.

## Editing package origin controls<a name="edit-package-origin-controls"></a>

Package origin controls are configured automatically based on how the first package version of a package is added to the repository, for more information see [Default package origin control settings](#default-package-origin-control-settings)\. To add or edit package origin controls for a package in a CodeArtifact repository, perform the steps in the following procedure\.

**To add or edit package origin controls \(console\)**

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. In the navigation pane, choose **Repositories**, and choose the repository that contains the package you want to edit\. 

1. In the **Packages** table, search for and select the package you want to edit\.

1. From the package summary page, in **Origin controls**, choose **Edit**\.

1. In **Edit origin controls**, choose the package origin controls you want to set for this package\. Both package origin control settings, Publish and Upstream, must be set at the same time\.
   + To allow publishing package versions directly, in **Publish**, choose **Allow**\. To block publishing of package versions, choose **Block**\.
   + To allow ingestion of packages from external repositories and pulling packages from upstream repositories, in **Upstream sources**, choose **Allow**\. To block all ingestion and pulling of package versions from external and upstream repositories, choose **Block**\.

**To add or edit package origin controls \(AWS CLI\)**

1. If you haven't, configure the AWS CLI by following the steps in [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md)\.

1. Use the `put-package-origin-configuration` command to add or edit package origin controls\. Replace the following fields:
   + Replace *my\_domain* with the CodeArtifact domain that contains the package you want to update\.
   + Replace *my\_repo* with the CodeArtifact repository that contains the package you want to update\.
   + Replace *npm* with the package format of the package you want to update\.
   + Replace *my\_package* with the name of the package you want to update\.
   + Replace *ALLOW* and *BLOCK* with your desired package origin control settings\.

   ```
   aws codeartifact put-package-origin-configuration --domain my_domain \
   --repository my_repo --format npm --package my_package \ 
   --restrictions publish=ALLOW,upstream=BLOCK
   ```

## Publishing and upstream repositories<a name="package-publishing-upstreams"></a>

CodeArtifact doesn't allow publishing package versions that are present in reachable upstream repositories or public repositories\. For example, suppose that you want to publish a Maven package `com.mycompany.mypackage:1.0` to a repository `myrepo`, and `myrepo` has an upstream repository with an external connection to Maven Central\. Consider the following scenarios\.

1. The package origin control settings on `com.mycompany.mypackage` are **Publish: ALLOW** and **Upstream: ALLOW**\. If `com.mycompany.mypackage:1.0` is present in the upstream repository or in Maven Central, CodeArtifact rejects any attempt to publish to it in `myrepo` with a 409 conflict error\. You could still publish a different version, such as `com.mycompany.mypackage:1.1`\.

1. The package origin control settings on `com.mycompany.mypackage` are **Publish: ALLOW** and **Upstream: BLOCK**\. You can publish any version of `com.mycompany.mypackage` to your repository that do not already exist because package versions are not reachable\.

1. The package origin control settings on `com.mycompany.mypackage` are **Publish: BLOCK** and **Upstream: ALLOW**\. You cannot publish any package versions directly to your repository\.