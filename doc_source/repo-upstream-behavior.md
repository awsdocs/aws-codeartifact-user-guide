# Requesting a package version with upstream repositories<a name="repo-upstream-behavior"></a>

 When a client \(for example, npm\) requests a package version from an CodeArtifact repository named `my-repo` that has multiple upstream repositories, the following can occur: 
+  If `my-repo` contains the requested package version, it is returned to the client\. 
+  If `my-repo` does not contain the requested package version, CodeArtifact looks for it in `my-repo`'s upstream repositories\. If the package version is found, a reference to it is copied to `my-repo`, and the package version is returned to the client\. 
+  If neither `my-repo` nor its upstream repositories contain the package version, an HTTP 404 `Not Found` response is returned to the client\.

 When you add upstream repositories using the `create-repository` or `update-repository` command, the order they are passed to the `--upstreams` parameter determines their priority when a package version is requested\. Specify upstream repositories with `--upstreams` in the order that you want CodeArtifact to use when a package version is requested\. For more information, see [Upstream repository priority order](repo-upstream-search-order.md)\. 

 The maximum number of direct upstream repositories allowed for one repository is 10\. The maximum number of repositories CodeArtifact looks in when a package version is requested is 20\. 

## Package retention from upstream repositories<a name="package-retention-upstream-repos"></a>

 If a requested package version is found in an upstream repository, a reference to it is retained and is always available from the downstream repository\. The retained package version is not affected by any of the following: 
+  Deleting the upstream repository\. 
+  Disconnecting the upstream repository from the downstream repository\. 
+  Deleting the package version from the upstream repository\. 
+  Editing the package version in the upstream repository \(for example, by adding a new asset to it\)\. 

## Fetch packages through an upstream relationship<a name="fetching-packages-through-an-upstream-relationship"></a>

If a CodeArtifact repository has an upstream relationship with a repository that has an external connection, requests for packages not in the upstream repository are copied from the external repository\. For example, consider the following configuration: a repository named `repo-A` has an upstream repository named `repo-B`\. `repo-B` has an external connection to [https://npmjs\.com](https://npmjs.com)\.

![\[Simple upstream repository diagram showing three repositories chained together.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple upstream repository diagram showing three repositories chained together.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple upstream repository diagram showing three repositories chained together.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

If `npm` is configured to use the `repo-A` repository, running `npm install` triggers the copying of packages from [https://npmjs\.com](https://npmjs.com) into `repo-B`\. The versions installed are also pulled into `repo-A`\. The following example installs `lodash`\.

```
$ npm config get registry
https://my-domain-domain-owner-id.d.codeartifact.us-west-2.amazonaws.com/npm/my-downstream-repo/
$ npm install lodash
+ lodash@4.17.15
added 1 package from 2 contributors in 6.933s
```

After running `npm install`, `repo-A` contains just the latest version \(`lodash 4.17.15`\) because that's the version that was fetched by `npm` from `repo-A`\.

```
aws codeartifact list-package-versions --repository repo-A --domain my-domain \
            --domain-owner domain-owner-id --format npm --package lodash
```

Example output:

```
{
    "package": "lodash",
    "format": "npm",
    "versions": [
        {
            "version": "4.17.15",
            "revision": "REVISION-1-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        }
    ]
}
```

 Because `repo-B` has an external connection to [https://npmjs\.com](https://npmjs.com), all the package versions that are imported from [https://npmjs\.com](https://npmjs.com) are stored in `repo-B`\. These package versions could have been fetched by any downstream repository with an upstream relationship to `repo-B`\. 

The contents of `repo-B` provide a way to see all the packages and package versions imported from [https://npmjs\.com](https://npmjs.com) over time\. For example, to see all the versions of the `lodash` package imported over time, you can use `list-package-versions`, as follows\.

```
aws codeartifact list-package-versions --repository repo-B --domain my-domain \
            --domain-owner domain-owner-id --format npm --package lodash --max-results 5
```

Example output:

```
{
    "package": "lodash",
    "format": "npm",
    "versions": [
        {
            "version": "0.10.0",
            "revision": "REVISION-1-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        },
        {
            "version": "0.2.2",
            "revision": "REVISION-2-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        },
        {
            "version": "0.2.0",
            "revision": "REVISION-3-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        },
        {
            "version": "0.2.1",
            "revision": "REVISION-4-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        },
        {
            "version": "0.1.0",
            "revision": "REVISION-5-SAMPLE-6C81EFF7DA55CC",
            "status": "Published"
        }
    ],
    "nextToken": "eyJsaXN0UGFja2FnZVZlcnNpb25zVG9rZW4iOiIwLjIuMiJ9"
}
```

## Package retention in intermediate repositories<a name="package-retention-intermediate-repositories"></a>

 CodeArtifact allows chaining upstream repositories\. For example, `repo-A` can have `repo-B` as an upstream and `repo-B` can have `repo-C` as an upstream\. This configuration makes the package versions in `repo-B` and `repo-C` available from `repo-A`\. 

![\[Simple upstream repository diagram showing three repositories chained together.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple upstream repository diagram showing three repositories chained together.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Simple upstream repository diagram showing three repositories chained together.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

 When a package manager connects to repository `repo-A` and fetches a package version from repository `repo-C`, the package version will not be retained in repository `repo-B`\. The package version will only be retained in the most\-downstream repository, in this example, `repo-A`\. It will not be retained in any intermediate repositories\. This is also true for longer chains; for example if there were four repositories `repo-A`, `repo-B`, `repo-C`, and `repo-D` and a package manager connected to `repo-A` fetched a package version from `repo-D`, the package version would be retained in `repo-A` but not in `repo-B` or `repo-C`\. 

 Package retention behavior is similar when pulling a package version from an external repository, except that the package version is always retained in the repository that has the external connection attached\. For example, `repo-A` has `repo-B` as an upstream\. `repo-B` has `repo-C` as an upstream, and `repo-C` also has **npmjs\.com** configured as an external connection; see the diagram below\.

![\[Upstream repository diagram showing three repositories chained together with an external connection to npmjs.com.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Upstream repository diagram showing three repositories chained together with an external connection to npmjs.com.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Upstream repository diagram showing three repositories chained together with an external connection to npmjs.com.\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

 If a package manager connected to `repo-A` requests a package version, *lodash 4\.17\.20* for example, and the package version is not present in any of the three repositories, it will be fetched from **npmjs\.com**\. When *lodash 4\.17\.20* is fetched, it will be retained in `repo-A` as that is the most\-downstream repository and `repo-C` as it has the external connection to **npmjs\.com** attached\. *lodash 4\.17\.20* will not be retained in `repo-B` as that is an intermediate repository\. 