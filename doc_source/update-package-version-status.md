# Update package version status<a name="update-package-version-status"></a>

Every package version in CodeArtifact has a status that describes the current state and availability of the package version\. You can change the package version status using both the AWS CLI and the console\. For more information on package version status, including a list of the available statuses, see [Package version status](packages-overview.md#package-version-status)\. 

## Updating package version status<a name="updating-pv-status"></a>

Setting the status of a package version allows controlling how a package version can be used without deleting it completely from the repository\. For example, when a package version has a status of `Unlisted`, it can still be downloaded as normal, but it will not appear in package version lists returned to commands such as `npm view`\. The [UpdatePackageVersionsStatus API](https://docs.aws.amazon.com/codeartifact/latest/APIReference/API_UpdatePackageVersionsStatus.html) allows setting the package version status of multiple versions of the same package in a single API call\. For a description of the different statuses, see [Packages overview](packages-overview.md)\.

Use the `update-package-versions-status` command to change the status of a package version to `Published`, `Unlisted`, or `Archived`\. To see the required IAM permissions to use the command, see [Required IAM permissions to update a package version status](#update-package-version-status-iam)\. The following example sets the status of version 4\.1\.0 of the npm package `chalk` to `Archived`\.

```
aws codeartifact update-package-versions-status --domain my_domain
 --domain-owner 111122223333 --repository my_repo --format npm --package chalk 
--versions 4.1.0 --target-status Archived
```

Sample output:

```
{
    "successfulVersions": {
        "4.1.0": {
            "revision": "+Oz8skWbwY3k8M6SrNIqNj6bVH/ax+CxvkJx+No5j8I=",
            "status": "Archived"
        }
    },
    "failedVersions": {}
}
```

This example uses an npm package, but the command works identically for other formats\. Multiple versions can be moved to the same target status using a single command, see the following example\.

```
aws codeartifact update-package-versions-status --domain my_domain
 --domain-owner 111122223333 --repository my_repo --format npm --package chalk 
--versions 4.1.0 4.1.1 --target-status Archived
```

Sample output:

```
{
    "successfulVersions": {
        "4.1.0": {
            "revision": "25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8vbzVMJ4=",
            "status": "Archived"
        },
        "4.1.1": {
            "revision": "+Oz8skWbwY3k8M6SrNIqNj6bVH/ax+CxvkJx+No5j8I=",
            "status": "Archived"
        }
    },
    "failedVersions": {}
}
```

Note that once published, a package version cannot be moved back to the `Unfinished` state, so this status is not permitted as a value for the `--target-status` parameter\. To move the package version to the `Disposed` state, use the `dispose-package-versions` command instead as described below\.

## Required IAM permissions to update a package version status<a name="update-package-version-status-iam"></a>

To call `update-package-versions-status` for a package, you must have the `codeartifact:UpdatePackageVersionsStatus` permission on the package resource\. This means you can grant permission to call `update-package-versions-status` on a per\-package basis\. For example, an IAM policy that grants permission to call `update-package-versions-status` on the npm package *chalk* would include a statement like the following\.

```
{
  "Action": [
    "codeartifact:UpdatePackageVersionsStatus"
  ],
  "Effect": "Allow",
  "Resource": "arn:aws:codeartifact:us-east-1:111122223333:package/my_domain/my_repo/npm//chalk"
}
```

## Updating status for a scoped npm package<a name="update-package-version-status-scoped-npm"></a>

To update the package version status of an npm package version with a scope, use the `--namespace` parameter\. For example, to unlist version 8\.0\.0 of `@nestjs/core`, use the following command\.

```
aws codeartifact update-package-versions-status --domain my_domain
 --domain-owner 111122223333 --repository my_repo --format npm --namespace nestjs 
--package core --versions 8.0.0 --target-status Unlisted
```

## Updating status for a Maven package<a name="update-package-version-status-maven"></a>

Maven packages always have a group ID, which is referred to as a namespace in CodeArtifact\. Use the `--namespace` parameter to specify the Maven group ID when calling `update-package-versions-status`\. For example, to archive version 2\.13\.1 of the Maven package `org.apache.logging.log4j:log4j`, use the following command\.

```
aws codeartifact update-package-versions-status --domain my_domain
 --domain-owner 111122223333 --repository my_repo --format maven 
--namespace org.apache.logging.log4j --package log4j 
--versions 2.13.1 --target-status Archived
```

## Specifying a package version revision<a name="update-status-specify-package-version-revision"></a>

A package version revision is a string that specifies a specific set of assets and metadata for a package version\. You can specify a package version revision to update the status of package versions that are in a specific state\. To specify a package version revision, use the `--version-revisions` parameter to pass one or more comma\-separated package versions and the package version revision pairs\. The status of a package version will only be updated if the current revision of the package version matches the value specified\.

**Note**  
The `—-versions` parameter must also be defined when using the `--version-revisions` parameter\.

```
aws codeartifact update-package-versions-status --domain my_domain 
   --domain-owner 111122223333 --repository my_repo --format npm --package chalk 
   --version-revisions "4.1.0=25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8bzVMJ4=" 
   --versions 4.1.0 --target-status Archived
```

To update multiple versions with a single command, pass a comma\-separated list of version and version revision pairs to the `--version-revisions` options\. The following example command defines two different package version and package version revision pairs\.

```
aws codeartifact update-package-versions-status --domain my_domain 
 --domain-owner 111122223333 --repository my_repo --format npm 
 --package chalk
 --version-revisions "4.1.0=25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8vbzVMJ4=,4.0.0=E3lhBp0RObRTut4pkjV5c1AQGkgSA7Oxtil6hMMzelc="  
 --versions 4.1.0 4.0.0 --target-status Published
```

Sample output:

```
{
    "successfulVersions": {
        "4.0.0": {
            "revision": "E3lhBp0RObRTut4pkjV5c1AQGkgSA7Oxtil6hMMzelc=",
            "status": "Published"
        },
        "4.1.0": {
            "revision": "25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8vbzVMJ4=",
            "status": "Published"
        }
    },
    "failedVersions": {}
}
```

When updating multiple package versions, the versions passed to `--version-revisions` must be the same as the versions passed to `--versions`\. If a revision is specified incorrectly, that version will not have its status updated\.

## Using the expected status parameter<a name="using-expected-status"></a>

The `update-package-versions-status` command provides the `--expected-status` parameter that supports specifying the expected current status of a package version\. If the current status does not match the value passed to `--expected-status`, the status of that package version will not be updated\.

For example, in *my\_repo*, versions 4\.0\.0 and 4\.1\.0 of the npm package `chalk` currently have a status of `Published`\. A call to `update-package-versions-status` that specifies an expected status of `Unlisted` will fail to update both package versions because of the status mismatch\.

```
aws codeartifact update-package-versions-status --domain my_domain
--domain-owner 111122223333 --repository my_repo --format npm --package chalk 
--versions 4.1.0 4.0.0 --target-status Archived --expected-status Unlisted
```

Sample output:

```
{
    "successfulVersions": {},
    "failedVersions": {
        "4.0.0": {
            "errorCode": "MISMATCHED_STATUS",
            "errorMessage": "current status: Published, expected status: Unlisted"
        },
        "4.1.0": {
            "errorCode": "MISMATCHED_STATUS",
            "errorMessage": "current status: Published, expected status: Unlisted"
        }
    }
}
```

## Errors with individual package versions<a name="update-package-version-status-errors"></a>

There are multiple reasons why the status of a package version will not be updated when calling `update-package-versions-status`\. For example, the package version revision may have been specified incorrectly, or the expected status does not match the current status\. In these cases, the version will be included in the `failedVersions` map in the API response\. If one version fails, other versions specified in the same call to `update-package-versions-status` might be skipped and not have their status updated\. Such versions will also be included in the `failedVersions` map with an `errorCode` of `SKIPPED`\.

In the current implementation of `update-package-versions-status`, if one or more versions cannot have their status changed, all other versions will be skipped\. That is, either all versions are updated successfully or no versions are updated\. This behavior is not guaranteed in the API contract; in the future, some versions might succeed while other versions fail in a single call to `update-package-versions-status`\.

The following example command includes an version status update failure caused by a package version revision mismatch\. That update failure causes another version status update call to be skipped\.

```
aws codeartifact update-package-versions-status --domain my_domain
  --domain-owner 111122223333 --repository my_repo 
  --format npm --package chalk 
  --version-revisions "4.1.0=25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8vbzVMJ=,4.0.0=E3lhBp0RObRTut4pkjV5c1AQGkgSA7Oxtil6hMMzelc=" 
  --versions 4.1.0 4.0.0 --target-status Archived
```

Sample output:

```
{
    "successfulVersions": {},
    "failedVersions": {
        "4.0.0": {
            "errorCode": "SKIPPED",
            "errorMessage": "version 4.0.0 is skipped"
        },
        "4.1.0": {
            "errorCode": "MISMATCHED_REVISION",
            "errorMessage": "current revision: 25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8vbzVMJ4=, expected revision: 25/UjBleHs1DZewk+zozoeqH/R80Rc9gL1P8vbzVMJ="
        }
    }
}
```

## Disposing of package versions<a name="dispose-package-versions"></a>

The `Disposed` package status has similar behavior to `Archived`, except that the package assets will be permanently deleted by CodeArtifact so that the domain owner’s account will no longer be billed for the asset storage\. To change the status of a package version to `Disposed`, use the `dispose-package-versions` command\. This capability is separate from `update-package-versions-status` because disposing of a package version is not reversible\. Because the package assets will be deleted, the version’s status cannot be changed back to `Archived`, `Unlisted`, or `Published`\. The only action that can be taken on a package version that has been disposed is for it to be deleted using the `delete-package-versions` command\.

To call `dispose-package-versions` successfully, the calling IAM principal must have the `codeartifact:DisposePackageVersions` permission on the package resource\.

The behavior of the `dispose-package-versions` command is similar to `update-package-versions-status`, including the behavior of the `--version-revisions ` and `--expected-status` options that are described in the [version revision](#update-status-specify-package-version-revision) and [expected status](#using-expected-status) sections\. For example, the following command attempts to dispose a package version but fails due to a mismatched expected status\.

```
aws codeartifact dispose-package-versions —domain my_domain --domain-owner 111122223333 
--repository my_repo --format npm --package chalk --versions 4.0.0 
--expected-status Unlisted
```

Sample output:

```
{
    "successfulVersions": {},
    "failedVersions": {
        "4.0.0": {
            "errorCode": "MISMATCHED_STATUS",
            "errorMessage": "current status: Published, expected status: Unlisted"
        }
    }
}
```

If the same command is run again with an `--expected-status` of `Published`, the disposal will succeed\.

```
aws codeartifact dispose-package-versions —domain my_domain --domain-owner 111122223333 
--repository my_repo --format npm --package chalk --versions 4.0.0 
--expected-status Published
```

Sample output:

```
{
    "successfulVersions": {
        "4.0.0": {
            "revision": "E3lhBp0RObRTut4pkjV5c1AQGkgSA7Oxtil6hMMzelc=",
            "status": "Disposed"
        }
    },
    "failedVersions": {}
}
```