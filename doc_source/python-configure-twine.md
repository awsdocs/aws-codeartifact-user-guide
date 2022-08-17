# Configure and use twine with CodeArtifact<a name="python-configure-twine"></a>

## Configure twine with the `login` command<a name="python-configure-twine-login"></a>

First, configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\. Then, use the CodeArtifact `login` command to fetch credentials and configure `twine` with them\.

**Note**  
If you are accessing a repository in a domain that you own, you don't need to include `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

To configure twine, run the following command\.

```
aws codeartifact login --tool twine --domain my_domain --domain-owner 111122223333 --repository my_repo
```

 `login` fetches an authorization token from CodeArtifact using your AWS credentials\. The `login` command configures `pip` for use with CodeArtifact by editing `~/.config/pip/pip.conf` to set the `index-url` to the repository specified by the `--repository` option\.

The default authorization period after calling `login` is 12 hours, and `login` must be called to periodically refresh the token\. For more information about the authorization token created with the `login` command, see [Tokens created with the `login` command](tokens-authentication.md#auth-token-login)\.

## Configure twine without the `login` command<a name="python-configure-twine-without-login"></a>

 If you cannot use the `login` command to configure `twine`, you can use the `~/.pypirc` file or environment variables\. To use the `~/.pypirc` file, add the following entries to it\. The password must be an auth token acquired by the `get-authorization-token` API\.

```
[distutils]
index-servers =
 codeartifact
[codeartifact]
repository = https://my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/pypi/my_repo/
password = auth-token
username = aws
```

To use environment variables, do the following\.

**Note**  
If you are accessing a repository in a domain that you own, you do not need to include the `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

```
export TWINE_USERNAME=aws
export TWINE_PASSWORD=`aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text`
export TWINE_REPOSITORY_URL=`aws codeartifact get-repository-endpoint --domain my_domain --domain-owner 111122223333 --repository my_repo --format pypi --query repositoryEndpoint --output text`
```

## Run twine<a name="python-run-twine"></a>

Before using `twine` to publish Python package assets, you must first configure CodeArtifact permissions and resources\.

1. Follow the steps in the [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md) section to configure your AWS account, tools, and permissions\.

1. Configure `twine` by following the steps in [Configure and use twine with CodeArtifact](#python-configure-twine)\.

After you configure `twine`, you can run `twine` commands\. Use the following command to publish Python package assets\.

```
twine upload --repository codeartifact mypackage-1.0.tgz
```

For information about how to build and package your Python application, see [Generating Distribution Archives](https://packaging.python.org/tutorials/packaging-projects/#generating-distribution-archives) on the Python Packaging Authority website\.