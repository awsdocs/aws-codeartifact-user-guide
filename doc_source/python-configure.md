# Configure clients with the login command<a name="python-configure"></a>

After you create a repository, you can use the `pip` client to install packages and the `twine` client to publish packages\.

First, configure your AWS credentials for use with the AWS CLI, as described in [Getting started with CodeArtifact](getting-started.md)\. Then, use the CodeArtifact `login` command to fetch credentials for use with `pip` or `twine`\.

**Note**  
If you are accessing a repository in a domain that you own, you don't need to include the `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

```
aws codeartifact login --tool pip | twine --domain my-domain --domain-owner domain-owner-id --repository my-repo
```

 `login` fetches an authorization token from CodeArtifact using your AWS credentials\. 

Depending on the value of the `--tool` option, the login command will:
+ Configure `pip` for use with CodeArtifact by editing `~/.config/pip/pip.conf` to set the `index-url` to the repository specified by the `--repository` option\.
+ Configure `twine` for use with CodeArtifact by editing `~/.pypirc` to create an `index-server` section for the repository specified by the `--repository` option\.

**Note**  
CodeArtifact authorization tokens that are created using the `login` command are valid for a period of 12 hours, so you must call `login` periodically to refresh the token\. The 12\-hour authorization period begins after `login` is called\.   
If `login` is called while assuming a role, the token lifetime is independent of the maximum session duration of the role\. For example, suppose that you call `sts assume-role` and specify a session duration of 15 minutes, and then call `login` to fetch a CodeArtifact authorization token\. The token is valid for the full 12\-hour period even though this is longer than the 15\-minute session duration\.   
For more information about controlling session duration, see [Using IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html) in the *IAM User Guide*\. Tokens created by calling the `GetAuthorizationToken` API can be customized to last any period of time up to 12 hours\. 