# Copy packages between repositories<a name="copy-package"></a>

You can copy package versions from one repository to another in CodeArtifact\. This can be helpful for scenarios such as package promotion workflows or sharing package versions between teams or projects\. The source and destination repositories must be in the same domain to copy package versions\.

## Required IAM permissions to copy packages<a name="copypackageversions-iam-actions"></a>

To copy package versions in CodeArtifact, the calling user must have the required IAM permissions and the resource\-based policy attached to the source and destination repositories must have the required permissions\. For more information about resource\-based permissions policies and CodeArtifact repositories, see [ Repository policies](repo-policies.md)\.

The user calling `copy-package-versions` must have the `ReadFromRepository` permission on the source repository and the `CopyPackageVersions` permission on the destination repository\.

The source repository must have the `ReadFromRepository` permission and the destination repository must have the `CopyPackageVersions` permission assigned to the IAM account or user copying packages\. The following policies are example repository policies to be added to the source repository or destination repository with the `put-repository-permissions-policy` command\. Replace *111122223333* with the ID of the account calling `copy-package-versions`\.

**Note**  
Calling `put-repository-permissions-policy` will replace the current repository policy if one exists\. You can use the `get-repository-permissions-policy` command to see if a policy exists, for more information see [Read a policy](repo-policies.md#reading-a-policy)\. If a policy does exist, you may want to add these permissions to it instead of replacing it\.

**Example source repository permissions policy**

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "codeartifact:ReadFromRepository"
            ],
            "Effect": "Allow",
            "Principal": {
                 "AWS": "arn:aws:iam::111122223333:root"
            },
            "Resource": "*"
        }
    ]
}
```

**Example destination repository permissions policy**

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "codeartifact:CopyPackageVersions"
            ],
            "Effect": "Allow",
            "Principal": {
                 "AWS": "arn:aws:iam::111122223333:root"
            },
            "Resource": "*"
        }
    ]
}
```

## Copy package versions<a name="copy-package-versions"></a>

Use the `copy-package-versions` command in CodeArtifact to copy one or more package versions from a source repository to a destination repository in the same domain\. The following example will copy versions 6\.0\.2 and 4\.0\.0 of an npm package named `my-package` from the `my_repo` repository to the `repo-2` repository\.

```
aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333 --source-repository my_repo \
 --destination-repository repo-2 --package my-package --format npm \
 --versions 6.0.2 4.0.0
```

You can copy multiple versions of the same package name in a single operation\. To copy versions of different package names, you must call `copy-package-versions` for each one\.

The previous command will produce the following output, assuming both versions could be copied successfully\.

```
{
   "successfulVersions": {
        "6.0.2": {
            "revision": "REVISION-1-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        },
        "4.0.0": {
            "revision": "REVISION-1-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        }
    },
    "failedVersions": {}
}
```

## Copy a package from upstream repositories<a name="copy-package-upstream"></a>

Normally, `copy-package-versions` only looks in the repository specified by the `--source-repository` option for versions to copy\. However, you can copy versions from both the source repository and its upstream repositories by using the `--include-from-upstream` option\. If you use the CodeArtifact SDK, call the `CopyPackageVersions` API with the `includeFromUpstream` parameter set to true\. For more information, see [Working with upstream repositories in CodeArtifact](repos-upstream.md)\.

## Copy a scoped npm package<a name="copying-a-scoped-npm-package"></a>

 To copy an npm package version in a scope, use the `--namespace` option to specify the scope\. For example, to copy the package `@types/react`, use `--namespace types`\. The `@` symbol must be omitted when using `--namespace`\.

```
aws codeartifact copy-package-versions  --domain my_domain --domain-owner 111122223333 --source-repository repo-1 \
 --destination-repository repo-2 --format npm --namespace my-namespace \
 --package my-package --versions 0.12.2
```

## Copy Maven package versions<a name="copying-a-maven-package"></a>

To copy Maven package versions between repositories, specify the package to copy by passing the Maven groupID with the `--namespace` option and the Maven artifactID with the `--name` option\. For example, to copy a single version of `com.google.guava:guava`:

```
 aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333  \
 --source-repository my_repo --destination-repository repo-2 --format maven --namespace com.google.guava \
 --package guava --versions 27.1-jre
```

If the package version is copied successfully, the output will be similar to the following\.

```
{
    "successfulVersions": {
        "27.1-jre": {
            "revision": "REVISION-1-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        }
    },
    "failedVersions": {}
}
```

## Versions that do not exist in the source repository<a name="versions-that-do-not-exist-in-the-source-repository"></a>

If you specify a version that does not exist in the source repository, the copy will fail\. If some versions exist in the source repository and some do not, all versions will fail to copy\. In the following example, version 0\.2\.0 of the `array-unique` npm package is present in the source repository, but version 5\.6\.7 is not:

```
aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333 \
         --source-repository my_repo --destination-repository repo-2 --format npm \
         --package array-unique --versions 0.2.0 5.6.7
```

The output in this scenario will be similar to the following\.

```
{
    "successfulVersions": {},
    "failedVersions": {
        "0.2.0": {
            "errorCode": "SKIPPED",
            "errorMessage": "Version 0.2.0 was skipped"
        },
        "5.6.7": {
            "errorCode": "NOT_FOUND",
            "errorMessage": "Could not find version 5.6.7"
        }
    }
}
```

The `SKIPPED` error code is used to indicate that the version was not copied to the destination repository because another version was not able to be copied\. 

## Versions that already exist in the destination repository<a name="versions-that-already-exist-in-the-destination-repository"></a>

 When a package version is copied to a repository where it already exists, CodeArtifact compares its package assets and package version level metadata in the two repositories\.

 If the package version assets and metadata are identical in the source and destination repositories, a copy is not performed but the operation is considered successful\. This means that `copy-package-versions` is idempotent\. When this occurs, the version that was already present in both the source and destination repositories will not be listed in the output of `copy-package-versions`\.

In the following example, two versions of the npm package `array-unique` are present in the source repository `repo-1`\. Version 0\.2\.1 is also present in the destination repository `dest-repo` and version 0\.2\.0 is not\.

```
aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333 \
         --source-repository my_repo --destination-repository repo-2 --format npm --package array-unique \
         --versions 0.2.1 0.2.0
```

The output in this scenario will be similar to the following\.

```
{
    "successfulVersions": {
        "0.2.0": {
            "revision": "Yad+B1QcBq2kdEVrx1E1vSfHJVh8Pr61hBUkoWPGWX0=",
            "status": "Published"
        }
    },
    "failedVersions": {}
}
```

Version 0\.2\.0 is listed in `successfulVersions` because it was successfully copied from the source to destination repository\. Version 0\.2\.1 is not shown in the output as it was already present in the destination repository\.

 If the package version assets or metadata differ in the source and destination repositories, the copy operation will fail\. You can use the `--allow-overwrite` parameter to force an overwrite\. 

If some versions exist in the destination repository and some do not, all versions will fail to copy\. In the following example, version 0\.3\.2 of the `array-unique` npm package is present in both the source and destination repositories, but the contents of the package version are different\. Version 0\.2\.1 is present in the source repository but not the destination\.

```
aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333 \
         --source-repository my_repo --destination-repository repo-2 --format npm --package array-unique \
         --versions 0.3.2 0.2.1
```

The output in this scenario will be similar to the following\.

```
{
    "successfulVersions": {},
    "failedVersions": {
        "0.2.1": {
            "errorCode": "SKIPPED",
            "errorMessage": "Version 0.2.1 was skipped"
        },
        "0.3.2": {
            "errorCode": "ALREADY_EXISTS",
            "errorMessage": "Version 0.3.2 already exists"
        }
    }
}
```

Version 0\.2\.1 is marked as `SKIPPED` because it was not copied to the destination repository\. Is was not copied because the copy of version 0\.3\.2 failed because it was already present in the destination repository, but not identical in the source and destination repositories\.

## Specifying a package version revision<a name="specify-package-version-revision"></a>

 A package version revision is a string that specifies a specific set of assets and metadata for a package version\. You can specify a package version revision to copy package versions that are in a specific state\. To specify a package version revision, use the `--version-revisions` parameter to pass one or more comma\-separated package version and the package version revision pairs to the `copy-package-versions` command\. 

**Note**  
You must specify the `--versions` or the `--version-revisions` parameter with `copy-package-versions`\. You cannot specify both\. 

The following example will only copy version 0\.3\.2 of the package `my-package` if it is present in the source repository with package version revision `REVISION-1-SAMPLE-6C81EFF7DA55CC`\.

```
aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333 --source-repository repo-1 \
 --destination-repository repo-2 --format npm --namespace my-namespace \
 --package my-package --version-revisions 0.3.2=REVISION-1-SAMPLE-6C81EFF7DA55CC
```

The following example copies two versions of package `my-package`, 0\.3\.2 and 0\.3\.13\. The copy will only succeed if in the source repository version 0\.3\.2 of `my-package` has revision `REVISION-1-SAMPLE-6C81EFF7DA55CC` and version 0\.3\.13 has revision `REVISION-2-SAMPLE-55C752BEE772FC`\.

```
aws codeartifact copy-package-versions --domain my_domain --domain-owner 111122223333 --source-repository repo-1 \
 --destination-repository repo-2 --format npm --namespace my-namespace \
 --package my-package --version-revisions 0.3.2=REVISION-1-SAMPLE-6C81EFF7DA55CC,0.3.13=REVISION-2-SAMPLE-55C752BEE772FC
```

 To find the revisions of a package version, use the `describe-package-version` or the `list-package-versions` command\. 

 For more information, see [Package version revision](codeartifact-concepts.md#welcome-concepts-package-version-revision) and [CopyPackageVersion](https://docs.aws.amazon.com/codeartifact/latest/APIReference/API_CopyPackageVersions.html) in the *CodeArtifact API Reference*\. 

## Copy npm packages<a name="copying-npm-packages"></a>

 For more information about `copy-package-versions` behavior with npm packages, see [npm tags and the CopyPackageVersions API](npm-tags.md#tags-and-cpv)\. 