# API behavior with upstream repositories<a name="upstream-repo-api-behavior"></a>

 When you call certain CodeArtifact APIs on repositories that are connected to upstream repositories, the behavior may be different depending on if the packages or package versions are stored in the target repository or the upstream repository\. The behavior of these APIs is documented below\. 

For more information on CodeArtifact APIs, see the [CodeArtifact API Reference](https://docs.aws.amazon.com/codeartifact/latest/APIReference/Welcome.html)\.

Most APIs that reference a package or package version will return a `ResourceNotFound` error if the specified package version is not present in the target repository\. This is true even if the package or package version is present in an upstream repository\. Effectively, upstream repositories are ignored when calling these APIs\. These APIs are:
+ DeletePackageVersions
+ DescribePackageVersion
+ GetPackageVersionAsset
+ GetPackageVersionReadme
+ ListPackages
+ ListPackageVersionAssets
+ ListPackageVersionDependencies
+ ListPackageVersions
+ UpdatePackageVersionsStatus

To demonstrate this behavior, we have two repositories: `target-repo` and `upstream-repo`\. `target-repo` is empty and has `upstream-repo` configured as an upstream repository\. `upstream-repo` contains the npm package `lodash`\.

When calling the `DescribePackageVersion` API on `upstream-repo`, which contains the `lodash` package, we get the following output:

```
{
    "packageVersion": {
        "format": "npm",
        "packageName": "lodash",
        "displayName": "lodash",
        "version": "4.17.20",
        "summary": "Lodash modular utilities.",
        "homePage": "https://lodash.com/",
        "sourceCodeRepository": "https://github.com/lodash/lodash.git",
        "publishedTime": "2020-10-14T11:06:10.370000-04:00",
        "licenses": [
            {
                "name": "MIT"
            }
        ],
        "revision": "Ciqe5/9yicvkJT13b5/LdLpCyE6fqA7poa9qp+FilPs=",
        "status": "Published"
    }
```

When calling the same API on `target-repo`, which is empty but has `upstream-repo` configured as an upstream, we get the following output:

```
An error occurred (ResourceNotFoundException) when calling the DescribePackageVersion operation: 
Package not found in repository. RepoId: repo-id, Package = PackageCoordinate{packageType=npm, packageName=lodash},
```

 The `CopyPackageVersions` API behaves differently\. By default, `CopyPackageVersions` API only copies package versions that are stored in the target repository\. If a package version is stored in the upstream repository but not in the target repository, it will not be copied\. To include package versions of packages that are stored only in the upstream repository, set the value of `includeFromUpstream` to `true` in your API request\. 

For more information on the `CopyPackageVersions` API, see [Copy packages between repositories](copy-package.md)\.