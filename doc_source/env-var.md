# Pass an auth token using an environment variable<a name="env-var"></a>

AWS CodeArtifact uses auth tokens vended by the `GetAuthorizationToken` API to authenticate and authorize requests from build tools such as Maven and Gradle\. You can store these auth tokens in an environment variable that can be read by a build tool to obtain the token it needs to fetch packages from an CodeArtifact repository or publish packages to it\. 

For security reasons, this approach is preferable to storing the token in a file where it might be read by other users or processes, or accidentally checked into source control\.

1. Configure your AWS credentials as described in [Install or upgrade and then configure the AWS CLI](get-set-up-install-cli.md)\. 

1. Set the `CODEARTIFACT_TOKEN` environment variable:
**Note**  
In some scenarios, you don't need to include the `--domain-owner` argument\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.
   + macOS or Linux:

     ```
     export CODEARTIFACT_TOKEN=`aws codeartifact get-authorization-token --domain my-domain --domain-owner domain-owner-id --query authorizationToken --output text`
     ```
   + Windows \(using default command shell\):

     ```
     for /f %i in ('aws codeartifact get-authorization-token --domain my-domain --domain-owner domain-owner-id --query authorizationToken --output text') do set CODEARTIFACT_TOKEN=%i
     ```
   + Windows PowerShell:

     ```
     $CODEARTIFACT_TOKEN = aws codeartifact get-authorization-token --domain my-domain --domain-owner domain-owner-id --query authorizationToken --output text
     ```

**Note**  
CodeArtifact authorization tokens are valid for a default period of 12 hours\. You can change how long a token is valid using the `--durationSeconds` argument \(max authorization period is 12 hours\)\. When the authorization period expires, you must call `get-authorization-token` to get another token\. The authorization period begins after `get-authorization-token` is called\.   
If `get-authorization-token` is called while assuming a role, the token lifetime is independent of the maximum session duration of the role\. For example, suppose that you call `sts assume-role` and specify a session duration of 15 minutes, and then call `get-authorization-token` to fetch a CodeArtifact authorization token\. The token is valid for the full authorization period even though this is longer than the 15\-minute session duration\.   
For more information about controlling session duration, see [Using IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html) in the *IAM User Guide*\. 