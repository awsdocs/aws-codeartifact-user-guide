# Download package version assets<a name="download-assets"></a>

An *asset* is an individual file \(for example, an npm `.tgz` file or Maven POM or JAR file\) stored in CodeArtifact that is associated with a package version\. You can download package assets using the `get-package-version-assets command`\. This allows you to retrieve assets without using a package manager client such as `npm` or `pip`\. To download an asset you must provide the asset's name which can be obtained using the `list-package-version-assets` command, for more information see [List package version assets](list-assets.md)\. The asset will be downloaded to local storage with a file name that you specify\.

The following example downloads the *guava\-27\.1\-jre\.jar* asset from the Maven package *com\.google\.guava:guava* with version *27\.1\-jre*\.

```
aws codeartifact get-package-version-asset --domain my_domain --domain-owner 111122223333 --repository my_repo \
    --format maven --namespace com.google.guava --package guava --package-version 27.1-jre \
    --asset guava-27.1-jre.jar \
    guava-27.1-jre.jar
```

 In this example, the file name was specified as *guava\-27\.1\-jre\.jar* by the last argument in the preceding command, so the downloaded asset will be named *guava\-27\.1\-jre\.jar*\. 

The output of the command will be:

```
{
    "assetName": "guava-27.1-jre.jar",
    "packageVersion": "27.1-jre",
    "packageVersionRevision": "YGp9ck2tmy03PGSxioclfYzQ0BfTLR9zzhQJtERv62I="
}
```

**Note**  
To download assets from a scoped npm package, include the scope in the `--namespace` option\. The `@` symbol must be omitted when using `--namespace`\. For example, if the scope is `@types`, use `--namespace types`\.

 Downloading assets using `get-package-version-asset` requires `codeartifact:GetPackageVersionAsset` permission on the package resource\. For more information about resource\-based permission policies, see [Resource\-based policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_resource-based) in the *AWS Identity and Access Management User Guide*\. 