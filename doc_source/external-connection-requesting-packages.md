# Requesting packages from external connections<a name="external-connection-requesting-packages"></a>

The following sections describe how to request a package from an external connection and expected CodeArtifact behavior when requesting a package\.

**Topics**
+ [Fetch packages from an external connection](#fetching-packages-from-a-public-repository)
+ [External connection latency](#external-connection-latency)
+ [CodeArtifact behavior when an external repository is not available](#external-connection-unavailable)
+ [Availability of new package versions](#new-pv-availability)

## Fetch packages from an external connection<a name="fetching-packages-from-a-public-repository"></a>

To fetch packages from an external connection once you've added it to your CodeArtifact repository as described in [Connect a CodeArtifact repository to a public repository](external-connection.md), configure your package manager to use your repository and install the packages\.

**Note**  
The following instructions use `npm`, to view configuration and usage instructions for other package types, see [Using CodeArtifact with Maven](using-maven.md), [Using CodeArtifact with NuGet](using-nuget.md), or [Using CodeArtifact with Python](using-python.md)\.

**To fetch packages from an external connection**

1. Configure and authenticate your package manager with your CodeArtifact repository\. For `npm`, use the following `aws codeartifact login` command\.

   ```
   aws codeartifact login --tool npm --domain my_domain --domain-owner 111122223333 --repository my_repo
   ```

1. Request the package from the public repository\. For `npm`, use the following `npm install` command, replacing *lodash* with the package you want to install\.

   ```
   npm install lodash
   ```

1. After the package has been copied into your CodeArtifact repository, you can use the `list-packages` and `list-package-versions` commands to view it\.

   ```
   aws codeartifact list-packages --domain my_domain --domain-owner 111122223333 --repository my_repo
   ```

   Example output:

   ```
   {
       "packages": [
           {
               "format": "npm",
               "package": "lodash"
           }
       ]
   }
   ```

   The `list-package-versions` command lists all versions of the package copied into your CodeArtifact repository\.

   ```
   aws codeartifact list-package-versions --domain my_domain --domain-owner 111122223333 --repository my_repo --format npm --package lodash
   ```

   Example output:

   ```
   {
       "defaultDisplayVersion: "1.2.5"
       "format": "npm",
       "package": "lodash",
       "namespace": null,
       "versions": [
           {
               "version": "1.2.5", 
               "revision": "REVISION-1-SAMPLE-6C81EFF7DA55CC",
               "status": "Published"
           }
       ]
   }
   ```

## External connection latency<a name="external-connection-latency"></a>

When fetching a package from a public repository using an external connection, there is a delay from when the package is fetched from the public repository and when it is stored in your CodeArtifact repository\. For example, say you have installed version 1\.2\.5 of the npm package "lodash" as described in [Fetch packages from an external connection](#fetching-packages-from-a-public-repository)\. Although the `npm install lodash` lodash command completed successfully, the package version might not appear in your CodeArtifact repository yet\. It typically takes around 3 minutes for the package version to appear in your repository, although occasionally it can take longer\.

Because of this latency, you might have successfully retrieved a package version, but might not yet be able to see the version in your repository in the CodeArtifact console or when calling the ListPackages and ListPackageVersions API operations\. Once CodeArtifact has asynchronously persisted the package version, it will be visible in the console and via API requests\.

## CodeArtifact behavior when an external repository is not available<a name="external-connection-unavailable"></a>

Occasionally, an external repository will experience an outage that means CodeArtifact cannot fetch packages from it, or fetching packages is much slower than normal\. When this occurs, package versions already pulled from an external repository \(e\.g\. **npmjs\.com**\) and stored in a CodeArtifact repository will continue to be available for download from CodeArtifact\. However, packages that are not already stored in CodeArtifact may not be available, even when an external connection to that repository has been configured\. For example, your CodeArtifact repository might contain the npm package version `lodash 4.17.19 ` because that's what you have been using in your application so far\. When you want to upgrade to `4.17.20`, normally CodeArtifact will fetch that new version from **npmjs\.com** and store it in your CodeArtifact repository\. However, if **npmjs\.com** is experiencing an outage this new version will not be available\. The only workaround is to try again later once **npmjs\.com** has recovered\.

External repository outages can also affect publishing new package versions to CodeArtifact\. In a repository with an external connection configured, CodeArtifact will not permit publishing a package version that is already present in the external repository, see [Packages overview](packages-overview.md) for more information\. However, an external repository outage may in rare cases mean that CodeArtifact does not have up\-to\-date information on which packages and package versions are present in an external repository\. In this case, CodeArtifact might permit a package version to be published that it would normally deny\.

## Availability of new package versions<a name="new-pv-availability"></a>

 For a package version in a public repository such as npmjs\.com or the Python Package Index \(PyPI\) to be available through a CodeArtifact repository, it must first be added to a regional package metadata cache\. This cache is maintained by CodeArtifact in each AWS region and contains metadata that describes the contents of supported public repositories\. Because of this cache, there is a delay between when a new package version is published to a public repository and when it is available from CodeArtifact\. This delay varies by package type\.

For npm and Nuget packages, there may be a delay of up to 10 minutes from when a new package version is published to npmjs\.com or nuget\.org and when it is available for installation from a CodeArtifact repository\. CodeArtifact automatically synchronizes metadata from these two repositories to ensure the cache is up to date\.

For Python and Maven packages, there may be a delay of up to 3 hours from when a new package version is published to a public repository and when it is available for installation from a CodeArtifact repository\. CodeArtifact will check for new versions of a package at most once every 3 hours\. The first request for a given package name after the 3\-hour cache lifetime has expired will cause all new versions of that package to be imported into the regional cache\.

This means that for Python and Maven packages that are in common use, new versions will typically be imported every 3 hours because the high rate of requests means that the cache will often be updated as soon as the cache lifetime has expired\. For infrequently used packages, the cache will not have the latest version until a version of the package is requested from a CodeArtifact repository\. On the first request, only previously imported versions will be available from CodeArtifact, but this request will cause the cache to be updated\. On subsequent requests, the new versions of the package will have been added to the cache and will be available for download\.