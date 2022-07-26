# View and update package version details and dependencies<a name="describe-package-version"></a>

You can view information about a package version, including dependencies, in CodeArtifact\. You can also update the status of a package version\. For more information on package version status, see [Package version status](packages-overview.md#package-version-status)\. 

## View package version details<a name="view-package-details"></a>

 Use the `describe-package-version` command to view details about package versions\. Package version details are extracted from a package when it is published to CodeArtifact\. The details in different packages vary and depend on their formats and how much information their authors added to them\. 

 Most information in the output of the `describe-package-version` command depends on the package format\. For example, `describe-package-version` extracts an npm package's information from its `package.json` file\. The revision is created by CodeArtifact\. For more information, see [Specifying a package version revision](copy-package.md#specify-package-version-revision)\. 

 Two package versions with the same name can be in the same repository if they each are in different namespaces\. Use the optional `--namespace` parameter to specify a namespace\. For more information, see [View npm package version details](#describe-package-version-npm) or [View Maven package version details](#describe-package-version-maven)\.

 The following example returns details about version `1.9.0` of a Python package named `pyhamcrest` that is in the `my_repo` repository\. 

```
aws codeartifact describe-package-version --domain my_domain --domain-owner 111122223333 --repository my_repo \
--format pypi --package pyhamcrest --package-version 1.9.0
```

 The output might look like the following\.

```
{
  "format": "pypi",
  "package": "PyHamcrest",
  "displayName": "PyHamcrest",
  "version": "1.9.0",
  "summary": "Hamcrest framework for matcher objects",
  "homePage": "https://github.com/hamcrest/PyHamcrest",
  "publishedTime": 1566002944.273,
  "licenses": [
    {
      "id": "license-id",
      "name": "license-name"
    }
  ],
  "revision": "REVISION-SAMPLE-55C752BEE9B772FC"
}
```

## View npm package version details<a name="describe-package-version-npm"></a>

To view details about an npm package version, set the value of the `--format` option to **npm**\. Optionally, include the package version namespace \(npm *scope*\) in the `--namespace` option\. The value for the `--namespace` option should not include the leading `@`\. To search for the namespace `@types`, set the value to *types*\.

The following returns details about version `4.41.5` of an npm package named `webpack` in the `@types` scope\. 

```
aws codeartifact describe-package-version --domain my_domain --domain-owner 111122223333 --repository my_repo \
--format npm --package webpack --namespace types --package-version 4.41.5
```

 The output might look like the following\. 

```
{
  "format": "npm",
  "namespace": "types",
  "package": "webpack",
  "displayName": "webpack",
  "version": "4.41.5",
  "summary": "Packs CommonJs/AMD modules for the browser. Allows ... further output omitted for brevity",
  "homePage": "https://github.com/webpack/webpack",
  "sourceCodeRepository": "https://github.com/webpack/webpack.git",
  "publishedTime": 1577481261.09,
  "licenses": [
    {
      "id": "license-id",
      "name": "license-name"
    }
  ],
  "revision": "REVISION-SAMPLE-55C752BEE9B772FC",
  "status": "Published",
  "origin": {
            "domainEntryPoint": {
                "externalConnectionName": "public:npmjs"
            },
            "originType": "EXTERNAL"
  }
}
```

## View Maven package version details<a name="describe-package-version-maven"></a>

To view details about a Maven package version, set the value of the `--format` option to `maven` and include the package version namespace in the `--namespace` option\.

 The following example returns details about version `1.2` of a Maven package named `commons-rng-client-api` that is in the `org.apache.commons` namespace and the `my_repo` repository\. 

```
aws codeartifact describe-package-version --domain my_domain --domain-owner 111122223333 --repository my_repo \
--format maven --namespace org.apache.commons --package commons-rng-client-api --package-version 1.2
```

 The output might look like the following\.

```
{
  "format": "maven",
  "namespace": "org.apache.commons",
  "package": "commons-rng-client-api",
  "displayName": "Apache Commons RNG Client API",
  "version": "1.2",
  "summary": "API for client code that uses random numbers generators.",
  "publishedTime": 1567920624.849,
  "licenses": [],
  "revision": "REVISION-SAMPLE-55C752BEE9B772FC"
}
```

## View package version dependencies<a name="view-package-dependencies"></a>

 Use the `list-package-version-dependencies` command to get a list of a package version's dependencies\. The following command lists the dependencies of an npm package named `my-package`, version `4.41.5`, in the `my_repo` repository, in the `my_domain` domain\. 

```
aws codeartifact list-package-version-dependencies --domain my_domain --domain-owner 111122223333 --repository my_repo \
--format npm --package my-package --package-version 4.41.5
```

 The output might look like the following\. 

```
{
  "dependencies": [
    {
      "namespace": "webassemblyjs",
      "package": "ast",
      "dependencyType": "regular",
      "versionRequirement": "1.8.5"
    },
    {
      "namespace": "webassemblyjs",
      "package": "helper-module-context",
      "dependencyType": "regular",
      "versionRequirement": "1.8.5"
    },
    {
      "namespace": "webassemblyjs",
      "package": "wasm-edit",
      "dependencyType": "regular",
      "versionRequirement": "1.8.5"
    }
  ],
  "versionRevision": "REVISION-SAMPLE-55C752BEE9B772FC"
}
```

## View package version readme file<a name="view-package-readme"></a>

 Some package formats, such as npm, include a `README` file\. Use the `get-package-version-readme` to get the `README` file of a package version\. The following command returns the `README` file of an npm package named `my-package`, version `4.41.5`, in the `my_repo` repository, in the `my_domain` domain\. 

**Note**  
CodeArtifact does not support displaying readme files from Maven packages\.

```
aws codeartifact get-package-version-readme --domain my_domain --domain-owner 111122223333 --repository my_repo \
--format npm --package my-package --package-version 4.41.5
```

 The output might look like the following\.

```
{  
  "format": "npm",
  "package": "my-package",
  "version": "4.41.5"
  "readme": "<div align=\"center\">\n   <a href=\https://github.com/webpack/webpack\"> ... more content ... \n",
  "versionRevision": "REVISION-SAMPLE-55C752BEE9B772FC"
}
```