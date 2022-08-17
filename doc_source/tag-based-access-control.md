# Using tags to control access to CodeArtifact resources<a name="tag-based-access-control"></a>

Conditions in IAM user policy statements are part of the syntax that you use to specify permissions to resources required by CodeArtifact actions\. Using tags in conditions is one way to control access to resources and requests\. For information about tagging CodeArtifact resources, see [Tagging resources](tag-resources.md)\. This topic discusses tag\-based access control\.

When you design IAM policies, you might be setting granular permissions by granting access to specific resources\. As the number of resources that you manage grows, this task becomes more difficult\. Tagging resources and using tags in policy statement conditions can make this task easier\. You grant access in bulk to any resource with a certain tag\. Then you repeatedly apply this tag to relevant resources, during creation or later\.

Tags can be attached to the resource or passed in the request to services that support tagging\. In CodeArtifact, resources can have tags, and some actions can include tags\. When you create an IAM policy, you can use tag condition keys to control:
+ Which users can perform actions on a domain or repository resource, based on tags that it already has\.
+ Which tags can be passed in an action's request\.
+ Whether specific tag keys can be used in a request\.

For the complete syntax and semantics of tag condition keys, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide*\.

The following examples demonstrate how to specify tag conditions in policies for CodeArtifact users\.

**Example 1: Limit actions based on tags in the request**  
The `AWSCodeArtifactAdminAccess` managed user policy gives users unlimited permission to perform any CodeArtifact action on any resource\.  
The following policy limits this power and denies unauthorized users permission to create repositories unless the request contains certain tags\. To do that, it denies the `CreateRepository` action if the request does not specify a tag named `costcenter` with one of the values `1` or `2`\. A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "codeartifact:CreateRepository",
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:RequestTag/costcenter": "true"
        }
      }
    },
    {
      "Effect": "Deny",
      "Action": "codeartifact:CreateRepository",
      "Resource": "*",
      "Condition": {
          "ForAnyValue:StringNotEquals": {
              "aws:RequestTag/costcenter": [
                  "1",
                  "2"
              ]
          }
      }
  }
  ]
}
```

**Example 2: Limit actions based on resource tags**  
The `AWSCodeArtifactAdminAccess` managed user policy gives users unlimited permission to perform any CodeArtifact action on any resource\.  
The following policy limits this power and denies unauthorized users permission to perform actions on repositories in specified domains\. To do that, it denies some actions if the resource has a tag named `Key1` with one of the values `Value1` or `Value2`\. \(The `aws:ResourceTag` condition key is used to control access to the resources based on the tags on those resources\.\) A customer's administrator must attach this IAM policy to unauthorized IAM users, in addition to the managed user policy\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "codeartifact:TagResource",
        "codeartifact:UntagResource",
        "codeartifact:DescribeDomain",
        "codeartifact:DescribeRepository",
        "codeartifact:PutDomainPermissionsPolicy",
        "codeartifact:PutRepositoryPermissionsPolicy",
        "codeartifact:ListRepositoriesInDomain",
        "codeartifact:UpdateRepository",
        "codeartifact:ReadFromRepository",
        "codeartifact:ListPackages",
        "codeartifact:ListTagsForResource"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Key1": ["Value1", "Value2"]
        }
      }
    }
  ]
}
```

**Example 3: Allow actions based on resource tags**  
The following policy grants users permission to perform actions on, and get information about, repositories and packages in CodeArtifact\.  
To do that, it allows specific actions if the repository has a tag named `Key1` with the value `Value1`\. \(The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\.\) The `aws:TagKeys` condition ensures tag key case sensitivity\. This policy is useful for IAM users who don't have the `AWSCodeArtifactAdminAccess` managed user policy attached\. The managed policy gives users unlimited permission to perform any CodeArtifact action on any resource\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codeartifact:UpdateRepository",
        "codeartifact:DeleteRepository",
        "codeartifact:ListPackages",
        "codeartifact:ListPackageVersions"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Key1": "Value1"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["Key1"]
        }
      }
    }
  ]
}
```

**Example 4: Allow actions based on tags in the request**  
The following policy grants users permission to create repositories in specified domains in CodeArtifact\.  
To do that, it allows the `CreateRepository` and `TagResource` actions if the create resource API in the request specifies a tag named `Key1` with the value `Value1`\. \(The `aws:RequestTag` condition key is used to control which tags can be passed in an IAM request\.\) The `aws:TagKeys` condition ensures tag key case sensitivity\. This policy is useful for IAM users who don't have the `AWSCodeArtifactAdminAccess` managed user policy attached\. The managed policy gives users unlimited permission to perform any CodeArtifact action on any resource\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codeartifact:CreateRepository",
        "codeartifact:TagResource"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/Key1": "Value1"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["Key1"]
        }
      }
    }
  ]
}
```