# NuGet package name, version, and asset name normalization<a name="nuget-name-normalization"></a>

CodeArtifact normalizes package and asset names and package versions before storing them, which means the names or versions in CodeArtifact may be different than the ones provided when the package or asset was published\.

**Package name normalization: ** CodeArtifact normalizes NuGet package names by converting all letters to lowercase\.

**Package version normalization: ** CodeArtifact normalizes NuGet package versions using the same pattern as NuGet\. The following information is from [Normalized version numbers](https://docs.microsoft.com/en-us/nuget/concepts/package-versioning#normalized-version-numbers) from the NuGet documentation\. 
+ Leading zeroes are removed from version numbers:
  + `1.00` is treated as `1.0`
  + `1.01.1` is treated as `1.1.1`
  + `1.00.0.1` is treated as `1.0.0.1`
+ A zero in the fourth part of the version number will be omitted:
  + `1.0.0.0` is treated as `1.0.0`
  + `1.0.01.0` is treated as `1.0.1`
+ SemVer 2\.0\.0 build metadata is removed:
  + `1.0.7+r3456` is treated as `1.0.7`

**Package asset name normalization: ** CodeArtifact constructs the NuGet package asset name from the normalized package name and package version\.

The non\-normalized package name and version name can be used with API and CLI requests because CodeArtifact performs normalization on the package name and version inputs for those requests\. For example, inputs of `--package Newtonsoft.JSON` and `--version 12.0.03.0` would be normalized and return a package that has a normalized package name of `newtonsoft.json` and version of `12.0.3`\.

You must use the normalized package asset name in API and CLI requests as CodeArtifact does not perform normalization on the `--asset` input\.

You must use normalized names and versions in ARNs\.

To find the normalized name of a package, use the `aws codeartifact list-packages` command\. For more information, see [List package names](list-packages.md)\.

To find the non\-normalized name of a package, use the `aws codeartifact describe-package-version` command\. The non\-normalized name of the package is returned in the `displayName` field\. For more information, see [View and update package version details and dependencies](describe-package-version.md)\.