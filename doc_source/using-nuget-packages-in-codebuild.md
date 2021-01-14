# Using NuGet packages in CodeBuild<a name="using-nuget-packages-in-codebuild"></a>

The steps below have been tested with the operating systems listed in the [Docker images provided by CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-available.html)\.

**Topics**
+ [Set up permissions with IAM roles](#nuget-packages-in-codebuild-iam)
+ [Consume NuGet packages](#consume-nuget-packages-in-codebuild)
+ [Build with NuGet packages](#build-nuget-packages-in-codebuild)
+ [Publish NuGet packages](#publish-nuget-packages-in-codebuild)

## Set up permissions with IAM roles<a name="nuget-packages-in-codebuild-iam"></a>

These steps are required when using NuGet packages from CodeArtifact in CodeBuild\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. On the **Roles** page, edit the role used by your CodeBuild build project\. This role must have the following permissions\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
         {
             "Effect": "Allow",
             "Action": [ "codeartifact:GetAuthorizationToken",
                         "codeartifact:GetRepositoryEndpoint",
                         "codeartifact:ReadFromRepository"
                         ],
             "Resource": "*"
         },
         {       
             "Effect": "Allow",
             "Action": "sts:GetServiceBearerToken",
             "Resource": "*",
             "Condition": {
                 "StringEquals": {
                     "sts:AWSServiceName": "codeartifact.amazonaws.com"
                 }
             }
         }
     ]
   }
   ```
**Important**  
 If you also want to use CodeBuild to publish packages, add the **codeartifact:PublishPackageVersion** permission\. 

   For information, see [Modifying a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_modify.html) in the *IAM User Guide*\.

## Consume NuGet packages<a name="consume-nuget-packages-in-codebuild"></a>

To consume NuGet packages from CodeBuild, include the following in your project's `buildspec.yaml` file\.

1. In the `install` section, install the CodeArtifact Credential Provider to configure command line tools such as `msbuild` and `dotnet` to build and publish packages to CodeArtifact\.

1. In the `pre-build` section, add your CodeArtifact repository to your NuGet configuration\.

See the `buildspec.yaml` examples below\. For more information, see [Using CodeArtifact with NuGet](using-nuget.md)\.

After the credential provider is installed and your repository source is added, you can run NuGet CLI tool commands from the `build` section to consume NuGet packages\.

### Linux<a name="consume-nuget-packages-in-codebuild-linux"></a>

 To consume NuGet packages using `dotnet`: 

```
version: 0.2

phases:
  install:
    runtime-versions:
      dotnet: latest
    commands:
      - export PATH="$PATH:/root/.dotnet/tools"
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      -  dotnet nuget add source -n codeartifact $(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)"v3/index.json"
  build:
    commands:
      - dotnet add packageName --source packageSourceName
```

### Windows<a name="consume-nuget-packages-in-codebuild-windows"></a>

 To consume NuGet packages using `dotnet`: 

```
version: 0.2

phases:
  install:
    commands:
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      - dotnet nuget add source -n codeartifact "$(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)v3/index.json"
  build:
    commands:
      - dotnet add packageName --source packageSourceName
```

## Build with NuGet packages<a name="build-nuget-packages-in-codebuild"></a>

To build with NuGet packages from CodeBuild, include the following in your project's `buildspec.yaml` file\.

1. In the `install` section, install the CodeArtifact Credential Provider to configure command line tools such as `msbuild` and `dotnet` to build and publish packages to CodeArtifact\.

1. In the `pre-build` section, add your CodeArtifact repository to your NuGet configuration\.

See the `buildspec.yaml` examples below\. For more information, see [Using CodeArtifact with NuGet](using-nuget.md)\.

After the credential provider is installed and your repository source is added, you can run NuGet CLI tool commands like `dotnet build` from the `build` section\.

### Linux<a name="build-nuget-packages-in-codebuild-linux"></a>

 To build NuGet packages using `dotnet`: 

```
version: 0.2

phases:
  install:
    runtime-versions:
      dotnet: latest
    commands:
      - export PATH="$PATH:/root/.dotnet/tools"
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      -  dotnet nuget add source -n codeartifact $(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)"v3/index.json"
  build:
    commands:
      - dotnet build
```

 To build NuGet packages using `msbuild`: 

```
version: 0.2

phases:
  install:
    runtime-versions:
      dotnet: latest
    commands:
      - export PATH="$PATH:/root/.dotnet/tools"
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      - dotnet nuget add source -n codeartifact $(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)"v3/index.json"
  build:
    commands:
      - msbuild -t:Rebuild -p:Configuration=Release
```

### Windows<a name="build-nuget-packages-in-codebuild-windows"></a>

 To build NuGet packages using `dotnet`: 

```
version: 0.2

phases:
  install:
    commands:
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      - dotnet nuget add source -n codeartifact "$(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)v3/index.json"
  build:
    commands:
      - dotnet build
```

 To build NuGet packages using `msbuild`: 

```
version: 0.2

phases:
  install:
    commands:
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      - dotnet nuget add source -n codeartifact "$(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)v3/index.json"
  build:
    commands:
      - msbuild -t:Rebuild -p:Configuration=Release
```

## Publish NuGet packages<a name="publish-nuget-packages-in-codebuild"></a>

To publish NuGet packages from CodeBuild, include the following in your project's `buildspec.yaml` file\.

1. In the `install` section, install the CodeArtifact Credential Provider to configure command line tools such as `msbuild` and `dotnet` to build and publish packages to CodeArtifact\.

1. In the `pre-build` section, add your CodeArtifact repository to your NuGet configuration\.

See the `buildspec.yaml` examples below\. For more information, see [Using CodeArtifact with NuGet](using-nuget.md)\.

After the credential provider is installed and your repository source is added, you can run NuGet CLI tool commands from the `build` section and publish your NuGet packages\.

### Linux<a name="publish-nuget-packages-in-codebuild-linux"></a>

 To publish NuGet packages using `dotnet`: 

```
version: 0.2

phases:
  install:
    runtime-versions:
      dotnet: latest
    commands:
      - export PATH="$PATH:/root/.dotnet/tools"
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      - dotnet nuget add source -n codeartifact $(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)"v3/index.json"
  build:
    commands:
      - dotnet pack -o .
      - dotnet nuget push *.nupkg -s codeartifact
```

### Windows<a name="publish-nuget-packages-in-codebuild-windows"></a>

 To publish NuGet packages using `dotnet`: 

```
version: 0.2

phases:
  install:
    commands:
      - dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
      - dotnet codeartifact-creds install
  pre_build:
    commands:
      - dotnet nuget add source -n codeartifact "$(aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget --query repositoryEndpoint --output text)v3/index.json"
  build:
    commands:
      - dotnet pack -o .
      - dotnet nuget push *.nupkg -s codeartifact
```