# Provision an IAM User<a name="get-set-up-provision-user"></a>

Follow these instructions to prepare an IAM user to use CodeArtifact\.

**To provision an IAM user**

1. Create an IAM user, or use one that is associated with your AWS account\. For more information, see [Creating an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html#Using_CreateUser_console) and [Overview of AWS IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/PoliciesOverview.html) in the *IAM User Guide*\.

1. Grant the IAM user access to CodeArtifact\.
   + **Option 1:** Create a custom IAM policy\. With a custom IAM policy, you can provide the minimum required permissions and change how long authentication tokens last\. See [Using identity\-based policies for AWS CodeArtifact](auth-and-access-control-iam-identity-based-access-control.md) for more information and example policies\.
   + **Option 2:** Use the `AWSCodeArtifactAdminAccess` AWS managed policy\. The contents of this policy are shown below\.
**Important**  
This policy grants access to all CodeArtifact APIs\. We recommend that you always use the minimum permissions required to accomplish your task\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

     ```
     {
        "Version": "2012-10-17",
        "Statement": [
           {
              "Action": [
                 "codeartifact:*"
              ],
              "Effect": "Allow",
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

The `sts:GetServiceBearerToken` permission is required to call the CodeArtifact `GetAuthorizationToken` API\. This API returns a token that must be used when using a package manager such as `npm` or `pip` with CodeArtifact\. To use a package manager with a CodeArtifact repository, your IAM user or role must Allow `sts:GetServiceBearerToken` as shown in the policy example above\.

If you haven't installed the package manager or build tool that you plan to use with CodeArtifact, see [Install your package manager or build tool](getting-started-install-package-manager.md)\.