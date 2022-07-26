# Configure and use Yarn with CodeArtifact<a name="npm-yarn"></a>

After you create a repository, you can use the Yarn client to manage npm packages\.

**Note**  
`Yarn 1.X` reads and uses information from your npm configuration file \(\.npmrc\), while `Yarn 2.X` does not\. The configuration for `Yarn 2.X` must be defined in the \.yarnrc\.yml file\.

**Contents**
+ [Configure Yarn 1\.X with the `aws codeartifact login` command](#npm-yarn-configure-login)
+ [Configure Yarn 2\.X with the `yarn config set` command](#npm-yarn-configure-yarn-command)

## Configure Yarn 1\.X with the `aws codeartifact login` command<a name="npm-yarn-configure-login"></a>

For `Yarn 1.X`, you can configure Yarn with CodeArtifact using the `aws codeartifact login` command\. The `login` command will configure your \~/\.npmrc file with your CodeArtifact repository endpoint information and credentials\. With `Yarn 1.X`, `yarn` commands use the configuration information from the \~/\.npmrc file\.

**To configure `Yarn 1.X` with the login command**

1. If you haven't done so already, configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\.

1. To run the `aws codeartifact login` command successfully, npm must be installed\. See [Downloading and installing Node\.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm/) in the *npm documentation* for installation instructions\.

1. Use the `aws codeartifact login` command to fetch CodeArtifact credentials and configure your \~/\.npmrc file\.
   + Replace *my\_domain* with your CodeArtifact domain name\.
   + Replace *111122223333* with the AWS account ID of the owner of the domain\. If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.
   + Replace *my\_repo* with your CodeArtifact repository name\.

   ```
   aws codeartifact login --tool npm --domain my_domain --domain-owner 111122223333 --repository my_repo
   ```

   The `login` command makes the following changes to your \~/\.npmrc file:
   + Adds an authorization token after fetching it from CodeArtifact using your AWS credentials\.
   + Sets the npm registry to the repository specified by the `--repository` option\.
   + **For npm 6 and lower:** Adds `"always-auth=true"` so the authorization token is sent for every npm command\.

   The default authorization period after calling `login` is 12 hours, and `login` must be called to refresh the token periodically\. For more information about the authorization token created with the `login` command, see [Tokens created with the `login` command](tokens-authentication.md#auth-token-login)\.

1. **For npm 7\.X** only, you must add `always-auth=true` to your \~/\.npmrc file to use Yarn\.

   1. Open your \~/\.npmrc file in a text editor and add `always-auth=true` on a new line\.

You can use the `yarn config list` command to check that Yarn is using the correct configuration\. After running the command, check the values in the `info npm config` section\. The contents should look similar to the following snippet\.

```
info npm config
{
  registry: 'https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/',
  '//my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/:_authToken': 'eyJ2ZXI...',
  'always-auth': true
}
```

## Configure Yarn 2\.X with the `yarn config set` command<a name="npm-yarn-configure-yarn-command"></a>

The following procedure details how to configure `Yarn 2.X` by updating your `.yarnrc.yml` configuration from the command line with the `yarn config set` command\.

**To update the `yarnrc.yml` configuration from the command line**

1. If you haven't done so already, configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\.

1. Use the `aws codeartifact get-repository-endpoint` command to get your CodeArtifact repository's endpoint\.
   + Replace *my\_domain* with your CodeArtifact domain name\.
   + Replace *111122223333* with the AWS account ID of the owner of the domain\. If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.
   + Replace *my\_repo* with your CodeArtifact repository name\.

   ```
   aws codeartifact get-repository-endpoint --domain my_domain --domain-owner 111122223333 --repository my_repo --format npm
   ```

1. Update the `npmRegistryServer` value in your \.yarnrc\.yml file with your repository endpoint\.

   ```
   yarn config set npmRegistryServer "https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/npm/my_repo/"
   ```

1. Fetch a CodeArtifact authorization token and store it in an environment variable\.
**Note**  
The following command is for macOS or Linux machines\. For information on configuring environment variables on a Windows machine, see [Pass an auth token using an environment variable](tokens-authentication.md#env-var)\.
   + Replace *my\_domain* with your CodeArtifact domain name\.
   + Replace *111122223333* with the AWS account ID of the owner of the domain\. If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.
   + Replace *my\_repo* with your CodeArtifact repository name\.

   ```
   CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text`
   ```

1. Use the `yarn config set` command to add your CodeArtifact authentication token to your \.yarnrc\.yml file\. Replace the URL in the following command with your repository endpoint URL from Step 2\.

   ```
   yarn config set 'npmRegistries["https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/npm/my_repo/"].npmAuthToken' "${CODEARTIFACT_AUTH_TOKEN}"
   ```

1. Use the `yarn config set` command to set the value of `npmAlwaysAuth` to `true`\. Replace the URL in the following command with your repository endpoint URL from Step 2\.

   ```
   yarn config set 'npmRegistries["https://my_domain-111122223333.d.codeartifact.region.amazonaws.com/npm/my_repo/"].npmAlwaysAuth' "true"
   ```

After configuring, your \.yarnrc\.yml configuration file should have contents similar to the following snippet\.

```
npmRegistries:
  "https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/":
    npmAlwaysAuth: true
    npmAuthToken: eyJ2ZXI...

npmRegistryServer: "https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/npm/my_repo/"
```

You can also use the `yarn config` command to check the values of `npmRegistries` and `npmRegistryServer`\.