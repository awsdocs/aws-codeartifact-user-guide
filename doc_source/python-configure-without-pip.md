# Configure pip without the login command<a name="python-configure-without-pip"></a>

 If you cannot use the `login` command to configure `pip`, you can use `pip config`\.

1. Use the AWS CLI to fetch a new authorization token\.
**Note**  
If you are accessing a repository in a domain that you own, you do not need to include the `--domain-owner`\. For more information, see [Cross\-account domains](domain-overview.md#domain-overview-cross-account)\.

   ```
   CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain my_domain --domain-owner 111122223333 --query authorizationToken --output text`
   ```

1. Use `pip config` to set the CodeArtifact registry URL and credentials\.

   ```
   pip config set global.index-url https://aws:$CODEARTIFACT_AUTH_TOKEN@my_domain-111122223333.d.codeartifact.region.amazonaws.com/pypi/my_repo/simple/
   ```

**Important**  
The registry URL must end with a forward slash \(/\)\. Otherwise, you cannot connect to the repository\.

**Example pip configuration file**

The following is an example of a `pip.conf` file after setting the CodeArtifact registry URL and credentials\.

```
[global]
index-url = https://aws:eyJ2ZX...@my_domain-111122223333.d.codeartifact.us-west-2.amazonaws.com/pypi/my_repo/simple/
```