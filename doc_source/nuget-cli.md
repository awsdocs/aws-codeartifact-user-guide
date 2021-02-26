# Use CodeArtifact with the nuget or dotnet CLI<a name="nuget-cli"></a>

You can use CLI tools like `nuget` and `dotnet` to publish and consume packages from CodeArtifact\. This document provides information about configuring the CLI tools and using them to publish or consume packages\.

**Topics**
+ [Configure the nuget or dotnet CLI](#nuget-configure-cli)
+ [Consume NuGet packages from CodeArtifact](#nuget-consume-cli)
+ [Publish NuGet packages to CodeArtifact](#nuget-publish-cli)
+ [CodeArtifact Credential Provider reference](#nuget-cred-provider-reference)

## Configure the nuget or dotnet CLI<a name="nuget-configure-cli"></a>

You can configure the nuget or dotnet CLI with a CodeArtifact Credential Provider, with the AWS CLI, or manually\. Configuring NuGet with the credential provider is highly recommended for simplified setup and continued authentication\.

### Method 1: Configure with the CodeArtifact Credential Provider<a name="nuget-configure-cli-cred-provider"></a>

The CodeArtifact Credential Provider simplifies the authentication and configuration of CodeArtifact with NuGet CLI tools\. CodeArtifact authentication tokens are valid for a maximum of 12 hours\. To avoid having to manually refresh the token while using the nuget or dotnet CLI, the credential provider periodically fetches a new token before the current token expires\. 

**Important**  
To use the credential provider, ensure that any existing AWS CodeArtifact credentials are cleared from your `nuget.config` file that may have been added manually or by running `aws codeartifact login` to configure NuGet previously\.

**Install and configure the CodeArtifact Credential Provider for NuGet**

------
#### [ dotnet ]

1. Download the [AWS\.CodeArtifact\.NuGet\.CredentialProvider tool from NuGet\.org](https://www.nuget.org/packages/AWS.CodeArtifact.NuGet.CredentialProvider) with the following `dotnet` command\.

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

Follow the steps below to use the NuGet CLI to install the CodeArtifact Credential Provider from an Amazon S3 bucket and configure it\. The credential provider will use the default AWS CLI profile, for more information on profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)\.

1. Download [AWS\.CodeArtifact\.NuGet\.CredentialProvider\.zip](https://a.co/dbGqKq7) from an Amazon S3 bucket\.

1. Unzip the file\.

1. Copy the **AWS\.CodeArtifact\.NuGetCredentialProvider** folder from the **netfx** folder to `%user_profile%/.nuget/plugins/netfx/` on Windows or `~/.nuget/plugins/netfx` on Linux or MacOS\.

1. Copy the **AWS\.CodeArtifact\.NuGetCredentialProvider** folder from the **netcore** folder to `%user_profile%/.nuget/plugins/netcore/` on Windows or `~/.nuget/plugins/netcore` on Linux or MacOS\.

------

After you create a repository and configure the credential provider you can use the `nuget` or `dotnet` CLI tools to install and publish packages\. For more information, see [Consume NuGet packages from CodeArtifact](#nuget-consume-cli) and [Publish NuGet packages to CodeArtifact](#nuget-publish-cli)\.

### Method 2: Configure nuget or dotnet with the login command<a name="nuget-configure-login"></a>

The `codeartifact login` command in the AWS CLI adds a repository endpoint and authorization token to your NuGet configuration file enabling nuget or dotnet to connect to your CodeArtifact repository\. This will modify the user\-level NuGet configuration which is located at `%appdata%\NuGet\NuGet.Config` for Windows and `~/.config/NuGet/NuGet.Config` or `~/.nuget/NuGet/NuGet.Config` for Mac/Linux\. For more information about NuGet configurations, see [Common NuGet configurations](https://docs.microsoft.com/en-us/nuget/consume-packages/configuring-nuget-behavior)\. 

**Configure nuget or dotnet with the `login` command**

1. Configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\.

1. On Windows, ensure that `nuget.exe` has been added to your `PATH`\. On MacOS, create an alias for nuget by running the `alias nuget="mono /usr/local/bin/nuget.exe"` command\.

1. Use the CodeArtifact `login` command to fetch credentials for use with NuGet\.
**Note**  
If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

   ```
   aws codeartifact login --tool nuget --domain my-domain --domain-owner domain-owner-id --repository my-repo
   ```

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
   aws codeartifact get-repository-endpoint --domain my-domain --domain-owner domain-owner-id --repository my-repo --format nuget
   ```

   Example output:

   ```
   {
      "repositoryEndpoint": "https://my-domain-domain-owner-id.d.codeartifact.us-west-2.amazonaws.com/nuget/my-repo/"
   }
   ```

1. Get an authorization token to connect to your repository from your package manager by using the `get-authorization-token` AWS CLI command\.

   ```
   aws codeartifact get-authorization-token --domain my-domain
   ```

   Example output:

   ```
   {
      "authorizationToken": "eyJ2I...viOw",
      "expiration": 1601616533.0
   }
   ```

1. Configure nuget or dotnet to use the repository endpoint from Step 1 and authorization token from Step 2\. Note, you will need to append `/v3/index.json` to your repository endpoint\.

------
#### [ dotnet ]

   ```
   dotnet nuget add source https://my-domain-domain-owner-id.d.codeartifact.us-west-2.amazonaws.com/nuget/my-repo/v3/index.json --name packageSourceName --password eyJ2I...viOw --username aws
   ```

------
#### [ nuget ]

   ```
   nuget sources add -name domain_name/repo_name -Source https://my-domain-domain-owner-id.d.codeartifact.us-west-2.amazonaws.com/nuget/my-repo/v3/index.json -password eyJ2I...viOw --username aws
   ```

------

   Example output:

   ```
   Package source with Name: domain_name/repo_name added successfully.
   ```

## Consume NuGet packages from CodeArtifact<a name="nuget-consume-cli"></a>

Once you have [configured NuGet with CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/nuget-cli.html), you can consume NuGet packages that are stored in your CodeArtifact repository or one of its upstream repositories\.

To consume a package version from a CodeArtifact repository or one of its upstream repositories with `nuget` or `dotnet`, run the following command replacing *packageName* with the name of the package you want to consume and *packageSourceName* with the source name for your CodeArtifact repository in your NuGet configuration file\. If you used the `login` command to configure your NuGet configuration, the source name is *domain\_name/repo\_name*\.

------
#### [ dotnet ]

```
dotnet add packageName --source packageSourceName
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
dotnet add packageName --version 1.0.0 --source packageSourceName
```

------
#### [ nuget ]

```
nuget install packageName -Version 1.0.0 -Source packageSourceName
```

------

See [Manage packages using the nuget\.exe CLI](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-nuget-cli) or [Install and manage packages using the dotnet CLI](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-dotnet-cli) in the *Microsoft Documentation* for more information\.

### Consume NuGet packages from NuGet\.org<a name="nuget-consume-nuget-gallery"></a>

You can consume NuGet packages from [NuGet\.org](https://www.nuget.org/) through a CodeArtifact repository by configuring the repository with an external connection to **NuGet\.org**\. Packages consumed from **NuGet\.org** are ingested and stored in your CodeArtifact repository\. For more information about adding external connections, see [Add an external connection](external-connection.md)\.

## Publish NuGet packages to CodeArtifact<a name="nuget-publish-cli"></a>

Once you have [configured NuGet with CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/nuget-cli.html), you can use `nuget` or `dotnet` to publish package versions to CodeArtifact repositories\.

To push a package version to a CodeArtifact repository, run the following command with the full path to your `.nupkg` file and the source name for your CodeArtifact repository in your NuGet configuration file\. If you used the `login` command to configure your NuGet configuration, the source name is `domain_name/repo_name`\.

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

## CodeArtifact Credential Provider reference<a name="nuget-cred-provider-reference"></a>

The CodeArtifact Credential Provider makes it easy to configure and authenticate NuGet with your CodeArtifact repositories\.

### CodeArtifact Credential Provider commands<a name="nuget-cred-provider-reference-commands"></a>

Below is the list of commands for the CodeArtifact Credential Provider\. These commands must be prefixed with `dotnet codeartifact-creds` like the following example\.

```
dotnet codeartifact-creds command
```
+ `configure set profile profile`: Configures the credential provider to use the provided AWS profile\.
+ `configure unset profile`: Removes the configured profile if set\.
+ `install`: Copies the credential provider to the `plugins` folder\.
+ `install --profile profile`: Copies the credential provider to the `plugins` folder and configures it to use the provided AWS profile\.
+ `uninstall`: Uninstalls the credential provider\. This does not remove the changes to the configuration file\.
+ `uninstall --delete-configuration`: Uninstalls the credential provider and removes all changes to the configuration file\.

### CodeArtifact Credential Provider logs<a name="nuget-cred-provider-reference-logs"></a>

To enable logging for the CodeArtifact Credential Provider, you must set the log file in your environment\. The credential provider logs contain helpful debugging information such as:
+ The AWS profile used to make connections
+ Any authentication errors
+ If the endpoint provided is not a CodeArtifact URL

**Set the CodeArtifact Credential Provider log file**

```
export AWS_CODEARTIFACT_NUGET_LOGFILE=/path/to/file
```

After the log file is set, any `codeartifact-creds` command will append its log output to the contents of that file\.
