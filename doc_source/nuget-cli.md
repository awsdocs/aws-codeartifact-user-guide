# Use CodeArtifact with the nuget or dotnet CLI<a name="nuget-cli"></a>

You can use CLI tools like `nuget` and `dotnet` to publish and consume packages from CodeArtifact\. This document provides information about configuring the CLI tools and using them to publish or consume packages\.

**Topics**
+ [Configure the nuget or dotnet CLI](#nuget-configure-cli)
+ [Consume NuGet packages from CodeArtifact](#nuget-consume-cli)
+ [Publish NuGet packages to CodeArtifact](#nuget-publish-cli)
+ [CodeArtifact NuGet Credential Provider reference](#nuget-cred-provider-reference)
+ [CodeArtifact NuGet Credential Provider versions](#nuget-cred-provider-history)

## Configure the nuget or dotnet CLI<a name="nuget-configure-cli"></a>

You can configure the nuget or dotnet CLI with the CodeArtifact NuGet Credential Provider, with the AWS CLI, or manually\. Configuring NuGet with the credential provider is highly recommended for simplified setup and continued authentication\.

### Method 1: Configure with the CodeArtifact NuGet Credential Provider<a name="nuget-configure-cli-cred-provider"></a>

The CodeArtifact NuGet Credential Provider simplifies the authentication and configuration of CodeArtifact with NuGet CLI tools\. CodeArtifact authentication tokens are valid for a maximum of 12 hours\. To avoid having to manually refresh the token while using the nuget or dotnet CLI, the credential provider periodically fetches a new token before the current token expires\. 

**Important**  
To use the credential provider, ensure that any existing AWS CodeArtifact credentials are cleared from your `nuget.config` file that may have been added manually or by running `aws codeartifact login` to configure NuGet previously\.

**Install and configure the CodeArtifact NuGet Credential Provider**

------
#### [ dotnet ]

1. Download the latest version of the [AWS\.CodeArtifact\.NuGet\.CredentialProvider tool from NuGet\.org](https://www.nuget.org/packages/AWS.CodeArtifact.NuGet.CredentialProvider) with the following `dotnet` command\.

   ```
   dotnet tool install -g AWS.CodeArtifact.NuGet.CredentialProvider
   ```

1. Use the `codeartifact-creds install` command to copy the credential provider to the NuGet plugins folder\.

   ```
   dotnet codeartifact-creds install
   ```

1. \(Optional\): Set the AWS profile you want to use with the credential provider\. If not set, the credential provider will use the default profile\. For more information on AWS CLI profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)\.

   ```
   dotnet codeartifact-creds configure set profile profile_name
   ```

------
#### [ nuget ]

Perform the following steps to use the NuGet CLI to install the CodeArtifact NuGet Credential Provider from an Amazon S3 bucket and configure it\. The credential provider will use the default AWS CLI profile, for more information on profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)\.

1. Download the latest version of the [CodeArtifact NuGet Credential Provider \(codeartifact\-nuget\-credentialprovider\.zip\)](https://a.co/dbGqKq7) from an Amazon S3 bucket\.

   To view and download earlier versions, see [CodeArtifact NuGet Credential Provider versions](#nuget-cred-provider-history)\.

1. Unzip the file\.

1. Copy the **AWS\.CodeArtifact\.NuGetCredentialProvider** folder from the **netfx** folder to `%user_profile%/.nuget/plugins/netfx/` on Windows or `~/.nuget/plugins/netfx` on Linux or MacOS\.

1. Copy the **AWS\.CodeArtifact\.NuGetCredentialProvider** folder from the **netcore** folder to `%user_profile%/.nuget/plugins/netcore/` on Windows or `~/.nuget/plugins/netcore` on Linux or MacOS\.

------

After you create a repository and configure the credential provider you can use the `nuget` or `dotnet` CLI tools to install and publish packages\. For more information, see [Consume NuGet packages from CodeArtifact](#nuget-consume-cli) and [Publish NuGet packages to CodeArtifact](#nuget-publish-cli)\.

### Method 2: Configure nuget or dotnet with the login command<a name="nuget-configure-login"></a>

The `codeartifact login` command in the AWS CLI adds a repository endpoint and authorization token to your NuGet configuration file enabling nuget or dotnet to connect to your CodeArtifact repository\. This will modify the user\-level NuGet configuration which is located at `%appdata%\NuGet\NuGet.Config` for Windows and `~/.config/NuGet/NuGet.Config` or `~/.nuget/NuGet/NuGet.Config` for Mac/Linux\. For more information about NuGet configurations, see [Common NuGet configurations](https://docs.microsoft.com/en-us/nuget/consume-packages/configuring-nuget-behavior)\. 

**Configure nuget or dotnet with the `login` command**

1. Configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\.

1. Ensure that the NuGet CLI tool \(`nuget` or `dotnet`\) has been properly installed and configured\. For instructions, see the [nuget](https://docs.microsoft.com/en-us/nuget/reference/nuget-exe-cli-reference) or [dotnet](https://docs.microsoft.com/en-us/dotnet/core/install/) documentation\.

1. Use the CodeArtifact `login` command to fetch credentials for use with NuGet\.
**Note**  
If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

------
#### [ dotnet ]

**Important**  
**Linux and MacOS users:** Because encryption is not supported on non\-Windows platforms, your fetched credentials will be stored as plain text in your configuration file\.

   ```
   aws codeartifact login --tool dotnet --domain my_domain --domain-owner 111122223333 --repository my_repo
   ```

------
#### [ nuget ]

   ```
   aws codeartifact login --tool nuget --domain my_domain --domain-owner 111122223333 --repository my_repo
   ```

------

The login command will:
+ Fetch an authorization token from CodeArtifact using your AWS credentials\. 
+ Update your user\-level NuGet configuration with a new entry for your NuGet package source\. The source that points to your CodeArtifact repository endpoint will be called `domain_name/repo_name`\.

The default authorization period after calling `login` is 12 hours, and `login` must be called to periodically refresh the token\. For more information about the authorization token created with the `login` command, see [Tokens created with the `login` command](tokens-authentication.md#auth-token-login)\.

After you create a repository and configure authentication you can use the `nuget`, `dotnet`, or `msbuild` CLI clients to install and publish packages\. For more information, see [Consume NuGet packages from CodeArtifact](#nuget-consume-cli) and [Publish NuGet packages to CodeArtifact](#nuget-publish-cli)\.

### Method 3: Configure nuget or dotnet without the login command<a name="nuget-configure-without-login"></a>

For manual configuration, you must add a repository endpoint and authorization token to your NuGet configuration file to enable nuget or dotnet to connect to your CodeArtifact repository\. 

**Manually configure nuget or dotnet to connect to your CodeArtifact repository\.**

1. Determine your CodeArtifact repository endpoint by using the `get-repository-endpoint` AWS CLI command\.

   ```
   aws codeartifact get-repository-endpoint --domain my_domain --domain-owner 111122223333 --repository my_repo --format nuget
   ```

   Example output:

   ```
   {
      "repositoryEndpoint": "https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/nuget/my_repo/"
   }
   ```

1. Get an authorization token to connect to your repository from your package manager by using the `get-authorization-token` AWS CLI command\.

   ```
   aws codeartifact get-authorization-token --domain my_domain
   ```

   Example output:

   ```
   {
      "authorizationToken": "eyJ2I...viOw",
      "expiration": 1601616533.0
   }
   ```

1. Create the full repository endpoint URL by appending `/v3/index.json` to the URL returned by `get-repository-endpoint` in step 3\.

1. Configure nuget or dotnet to use the repository endpoint from Step 1 and authorization token from Step 2\.
**Note**  
The source URL must end in `/v3/index.json` for nuget or dotnet to successfully connect to a CodeArtifact repository\.

------
#### [ dotnet ]

   **Linux and MacOS users:** Because encryption is not supported on non\-Windows platforms, you must add the `--store-password-in-clear-text` flag to the following command\. Note that this will store your password as plain text in your configuration file\.

   ```
   dotnet nuget add source https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/nuget/my_repo/v3/index.json --name packageSourceName --password eyJ2I...viOw --username aws
   ```

**Note**  
To update an existing source, use the `dotnet nuget update source` command\.

------
#### [ nuget ]

   ```
   nuget sources add -name domain_name/repo_name -Source https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/nuget/my_repo/v3/index.json -password eyJ2I...viOw -username aws
   ```

------

   Example output:

   ```
   Package source with Name: domain_name/repo_name added successfully.
   ```

## Consume NuGet packages from CodeArtifact<a name="nuget-consume-cli"></a>

Once you have [configured NuGet with CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/nuget-cli.html), you can consume NuGet packages that are stored in your CodeArtifact repository or one of its upstream repositories\.

To consume a package version from a CodeArtifact repository or one of its upstream repositories with `nuget` or `dotnet`, run the following command replacing *packageName* with the name of the package you want to consume and *packageSourceName* with the source name for your CodeArtifact repository in your NuGet configuration file\. If you used the `login` command to configure your NuGet configuration, the source name is *domain\_name/repo\_name*\.

**Note**  
When a package is requested, the NuGet client caches which versions of that package exists\. Because of this behavior, an install may fail for a package that was requested before it was available\. To avoid this failure and successfully install a package that exists, you can either clear the NuGet cache ahead of an install with `nuget locals all --clear` or use the `--no-cache` option when running `nuget install` or `nuget restore`\.

------
#### [ dotnet ]

```
dotnet add package packageName --source packageSourceName
```

------
#### [ nuget ]

```
nuget install packageName -Source packageSourceName
```

------

**To install a specific version of a package**

------
#### [ dotnet ]

```
dotnet add package packageName --version 1.0.0 --source packageSourceName
```

------
#### [ nuget ]

```
nuget install packageName -Version 1.0.0 -Source packageSourceName
```

------

See [Manage packages using the nuget\.exe CLI](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-nuget-cli) or [Install and manage packages using the dotnet CLI](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-dotnet-cli) in the *Microsoft Documentation* for more information\.

### Consume NuGet packages from NuGet\.org<a name="nuget-consume-nuget-gallery"></a>

You can consume NuGet packages from [NuGet\.org](https://www.nuget.org/) through a CodeArtifact repository by configuring the repository with an external connection to **NuGet\.org**\. Packages consumed from **NuGet\.org** are ingested and stored in your CodeArtifact repository\. For more information about adding external connections, see [Connect a CodeArtifact repository to a public repository](external-connection.md)\.

## Publish NuGet packages to CodeArtifact<a name="nuget-publish-cli"></a>

Once you have [configured NuGet with CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/nuget-cli.html), you can use `nuget` or `dotnet` to publish package versions to CodeArtifact repositories\.

To push a package version to a CodeArtifact repository, run the following command with the full path to your `.nupkg` file and the source name for your CodeArtifact repository in your NuGet configuration file\. If you used the `login` command to configure your NuGet configuration, the source name is `domain_name/repo_name`\.

**Note**  
You can create a NuGet package if you do not have one to publish\. For more information, see [Package creation workflow](https://docs.microsoft.com/en-us/nuget/create-packages/overview-and-workflow) in the *Microsoft documentation*\.

------
#### [ dotnet ]

```
dotnet nuget push path/to/nupkg/SamplePackage.1.0.0.nupkg --source packageSourceName
```

------
#### [ nuget ]

```
nuget push path/to/nupkg/SamplePackage.1.0.0.nupkg -Source packageSourceName
```

------

## CodeArtifact NuGet Credential Provider reference<a name="nuget-cred-provider-reference"></a>

The CodeArtifact NuGet Credential Provider makes it easy to configure and authenticate NuGet with your CodeArtifact repositories\.

### CodeArtifact NuGet Credential Provider commands<a name="nuget-cred-provider-reference-commands"></a>

This section includes the list of commands for the CodeArtifact NuGet Credential Provider\. These commands must be prefixed with `dotnet codeartifact-creds` like the following example\.

```
dotnet codeartifact-creds command
```
+ `configure set profile profile`: Configures the credential provider to use the provided AWS profile\.
+ `configure unset profile`: Removes the configured profile if set\.
+ `install`: Copies the credential provider to the `plugins` folder\.
+ `install --profile profile`: Copies the credential provider to the `plugins` folder and configures it to use the provided AWS profile\.
+ `uninstall`: Uninstalls the credential provider\. This does not remove the changes to the configuration file\.
+ `uninstall --delete-configuration`: Uninstalls the credential provider and removes all changes to the configuration file\.

### CodeArtifact NuGet Credential Provider logs<a name="nuget-cred-provider-reference-logs"></a>

To enable logging for the CodeArtifact NuGet Credential Provider, you must set the log file in your environment\. The credential provider logs contain helpful debugging information such as:
+ The AWS profile used to make connections
+ Any authentication errors
+ If the endpoint provided is not a CodeArtifact URL

**Set the CodeArtifact NuGet Credential Provider log file**

```
export AWS_CODEARTIFACT_NUGET_LOGFILE=/path/to/file
```

After the log file is set, any `codeartifact-creds` command will append its log output to the contents of that file\.

## CodeArtifact NuGet Credential Provider versions<a name="nuget-cred-provider-history"></a>

The following table contains version history information and download links for the CodeArtifact NuGet Credential Provider\.


| Version | Changes | Date published | Download link \(S3\) | 
| --- | --- | --- | --- | 
|  1\.0\.1 \(latest\)  |  Added support for net5, net6, and SSO profiles  |  03/05/2022  |  [Download v1\.0\.1](https://a.co/cAIkhV1)  | 
|  1\.0\.0  |  Initial CodeArtifact NuGet Credential Provider release  |  11/20/2020  |  [Download v1\.0\.0](https://a.co/8b2cENb)  | 