# Configure and use pip with CodeArtifact<a name="python-configure-pip"></a>

To use `pip` to install Python packages from your CodeArtifact repository, you must first configure the pip client with your CodeArtifact repository information and credentials\.

## Configure pip with the `login` command<a name="python-configure-pip-login"></a>

First, configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\. Then, use the CodeArtifact `login` command to fetch credentials and configure `pip` with them\.

**Note**  
If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

To configure pip, run the following command\.

```
aws codeartifact login --tool pip --domain my_domain --domain-owner 111122223333 --repository my_repo
```

 `login` fetches an authorization token from CodeArtifact using your AWS credentials\. The `login` command will configure `pip` for use with CodeArtifact by editing `~/.config/pip/pip.conf` to set the `index-url` to the repository specified by the `--repository` option\.

The default authorization period after calling `login` is 12 hours, and `login` must be called to periodically refresh the token\. For more information about the authorization token created with the `login` command, see [Tokens created with the `login` command](tokens-authentication.md#auth-token-login)\.

## Configure pip without the login command<a name="python-configure-without-pip"></a>

 If you cannot use the `login` command to configure `pip`, you can use `pip config`\.

1. Use the AWS CLI to fetch a new authorization token\.
**Note**  
If you are accessing a repository in a domain that you own, you do not need to include the `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

   ```
   CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text`
   ```

1. Use `pip config` to set the CodeArtifact registry URL and credentials\. The following command will update the current environment configuration file only\. To update the system\-wide configuration file, replace `site` with `global`\.

   ```
   pip config set site.index-url https://aws:$CODEARTIFACT_AUTH_TOKEN@my_domain-111122223333.d.codeartifact.region.amazonaws.com/pypi/my_repo/simple/
   ```

**Important**  
The registry URL must end with a forward slash \(/\)\. Otherwise, you cannot connect to the repository\.

**Example pip configuration file**

The following is an example of a `pip.conf` file after setting the CodeArtifact registry URL and credentials\.

```
[global]
index-url = https://aws:eyJ2ZX...@my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/pypi/my_repo/simple/
```

## Run pip<a name="python-run-pip"></a>

To run `pip` commands, you must configure `pip` with CodeArtifact\. For more information, see the following documentation\.

1. Follow the steps in the [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md) section to configure your AWS account, tools, and permissions\.

1. Configure `twine` by following the steps in [Configure and use twine with CodeArtifact](python-configure-twine.md)\.

Assuming that a package is present in your repository or one of its upstream repositories, you can install it with `pip install`\. For example, use the following command to install the `requests` package\.

```
pip install requests
```

Use the `-i` option to temporarily revert to installing packages from [https://pypi\.org](https://pypi.org) instead of your CodeArtifact repository\.

```
pip install -i https://pypi.org/simple requests
```