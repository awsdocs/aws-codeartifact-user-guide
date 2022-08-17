# Using identity\-based policies for AWS CodeArtifact<a name="auth-and-access-control-iam-identity-based-access-control"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on AWS CodeArtifact resources\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available to manage access to your CodeArtifact resources\. For more information, see [Overview of managing access permissions to your AWS CodeArtifact resources](auth-and-access-control-iam-access-control-identity-based.md)\.

**Topics**
+ [Policy examples](#policy-examples)
+ [Permissions required to use the AWS CodeArtifact console](#console-permissions)
+ [AWS managed \(predefined\) policies for AWS CodeArtifact](#managed-policies)
+ [Limit authorization token duration](#limit-token-duration)

## Policy examples<a name="policy-examples"></a>

The following IAM policy examples provide access to specific CodeArtifact actions and resources\. Some policies include the `sts:GetServiceBearerToken` permission, which is required to call the `GetAuthorizationToken` API\.

**Allow a user to get information about repositories and domains**

The following policy allows an IAM user or role to list and describe any type of CodeArtifact resource, including domains, repositories, packages, and assets\. The policy also includes the `codeArtifact:ReadFromRepository` permission, which allows the principal to fetch packages from a CodeArtifact repository\. It does not allow creating new domains or repositories and does not allow publishing new packages\.

The `codeartifact:GetAuthorizationToken` and `sts:GetServiceBearerToken` permissions are required to call the `GetAuthorizationToken` API\.

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Action": [
            "codeartifact:List*",
            "codeartifact:Describe*",
            "codeartifact:Get*",
            "codeartifact:Read*"
         ],
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

**Allow a user to get information about specific domains**

The following shows an example of a permissions policy that allows a user to list domains only in the `us-east-2` region for account `123456789012` for any domain that starts with the name `my`\.

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

**Allow a user to get information about specific repositories**

The following shows an example of a permissions policy that allows a user to get information about repositories that end with `test`, including information about the packages in them\. The user will not be able to publish, create, or delete resources\.

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Action": [
            "codeartifact:List*",
            "codeartifact:Describe*",
            "codeartifact:Get*",
            "codeartifact:Read*"
         ],
         "Resource": "arn:aws:codeartifact:*:*:repository/*/*test"      
      },
      {
         "Effect": "Allow",
         "Action": [
            "codeartifact:List*",
            "codeartifact:Describe*"
         ],
         "Resource": "arn:aws:codeartifact:*:*:package/*/*test/*/*/*"   
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
      },
      {
         "Effect": "Allow",
         "Action": "codeartifact:GetAuthorizationToken",
         "Resource": "*"
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
+ `AWSCodeArtifactReadOnlyAccess` – Provides read\-only access to CodeArtifact\.

  ```
  {
     "Version": "2012-10-17",
     "Statement": [
        {
           "Action": [
              "codeartifact:Describe*",
              "codeartifact:Get*",
              "codeartifact:List*",
              "codeartifact:ReadFromRepository"
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

To create and manage CodeArtifact service roles, you must also attach the AWS managed policy named `IAMFullAccess`\.

You can also create your own custom IAM policies to allow permissions for CodeArtifact actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\.

## Limit authorization token duration<a name="limit-token-duration"></a>

Users must authenticate to CodeArtifact with authorization tokens to publish or consume package versions\. Authorization tokens are valid only during their configured lifetime\. Tokens have a default lifetime of 12 hours\. For more information on authorization tokens, see [AWS CodeArtifact authentication and tokens](tokens-authentication.md)\.

When fetching a token, users can configure the lifetime of the token\. Valid values for the lifetime of an authorization token are `0`, and any number between `900` \(15 minutes\) and `43200` \(12 hours\)\. A value of `0` will create a token with a duration equal to the user's role's temporary credentials\.

Administrators can limit the valid values for the lifetime of an authorization token by using the `sts:DurationSeconds` condition key in the permissions policy attached to the user or group\. If the user attempts to create an authorization token with a lifetime outside of the valid values, the token creation will fail\.

The following example policies limit the possible durations of an authorization token created by CodeArtifact users\.

**Example policy: Limit token lifetime to exactly 12 hours \(43200 seconds\)**

With this policy, users will only be able to create authorization tokens with a lifetime of 12 hours\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "codeartifact:*",
            "Resource": "*"
        },
        {
            "Sid": "sts",
            "Effect": "Allow",
            "Action": "sts:GetServiceBearerToken",
            "Resource": "*",
            "Condition": {
                "NumericEquals": {
                    "sts:DurationSeconds": 43200
                },
                "StringEquals": {
                    "sts:AWSServiceName": "codeartifact.amazonaws.com"
                }
            }
        }
    ]
}
```

**Example policy: Limit token lifetime between 15 minutes and 1 hour, or equal to the user's temporary credentials period**

With this policy, users will be able to create tokens that are valid between 15 minutes and 1 hour\. Users will also be able to create a token that lasts the duration of their role's temporary credentials by specifying `0` for `--durationSeconds`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "codeartifact:*",
            "Resource": "*"
        },
        {
            "Sid": "sts",
            "Effect": "Allow",
            "Action": "sts:GetServiceBearerToken",
            "Resource": "*",
            "Condition": {
                "NumericLessThanEquals": {
                    "sts:DurationSeconds": 3600
                },
                "StringEquals": {
                    "sts:AWSServiceName": "codeartifact.amazonaws.com"
                }
            }
        }
    ]
}
```