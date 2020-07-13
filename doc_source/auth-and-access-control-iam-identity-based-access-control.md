# Using identity\-based policies for AWS CodeArtifact<a name="auth-and-access-control-iam-identity-based-access-control"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on AWS CodeArtifact resources\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available to manage access to your CodeArtifact resources\. For more information, see [Overview of managing access permissions to your AWS CodeArtifact resources](auth-and-access-control-iam-access-control-identity-based.md)\.

**Topics**
+ [Permissions required to use the AWS CodeArtifact console](#console-permissions)
+ [AWS managed \(predefined\) policies for AWS CodeArtifact](#managed-policies)

The following shows an example of a permissions policy that allows a user to get information about domains only in the `us-east-2` Region for account `123456789012` for any domain that starts with the name `my`\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "codeartifact:ListDomains",
      "Resource": "arn:aws:codeartifact:us-east-2:123456789012:domain/my*"      
    }
  ]
}
```

## Permissions required to use the AWS CodeArtifact console<a name="console-permissions"></a>

A user who uses the AWS CodeArtifact console must have a minimum set of permissions that allows the user to describe other AWS resources for the AWS account\. You must have permissions from the following services:
+ AWS CodeArtifact
+ AWS Key Management Service \(AWS KMS\)

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended\.

## AWS managed \(predefined\) policies for AWS CodeArtifact<a name="managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. These AWS managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to AWS CodeArtifact\.
+ `AWSCodeArtifactAdminAccess` – Provides full access to CodeArtifact including permissions to administrate CodeArtifact domains\. 
+ `AWSCodeArtifactReadOnlyAccess` – Provides read\-only access to CodeArtifact\.

To create and manage CodeArtifact service roles, you must also attach the AWS managed policy named `IAMFullAccess`\.

You can also create your own custom IAM policies to allow permissions for CodeArtifact actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\.