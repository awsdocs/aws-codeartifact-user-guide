# Create a repository<a name="create-repo"></a>

You can create a repository using the CodeArtifact console or the AWS Command Line Interface \(AWS CLI\)\. When you create a repository, it does not contain any packages\. Each repository is associated with the AWS account that you use when you create it\. An AWS account can have up to 10,000 repositories\. For more information on CodeArtifact service limits, see [Quotas in AWS CodeArtifact](service-limits.md)\. You can delete repositories to make room for more\. 

Repositories are polyglot—a single repository can contain packages of any supported type\.

A repository can have one or more CodeArtifact repositories associated with it as upstream repositories\. This allows a package manager client to access the packages contained in more than one repository using a single URL endpoint\. For more information, see [Working with upstream repositories in CodeArtifact](repos-upstream.md)\. 

**Note**  
After you create a repository, you cannot change its name, associated AWS account, or domain\.

**Topics**
+ [Create a repository \(console\)](#create-repo-console)
+ [Create a repository \(AWS CLI\)](#create-repo-cli)
+ [Create a repository with an upstream repository](#creating-a-repository-with-an-upstream)

## Create a repository \(console\)<a name="create-repo-console"></a>

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1.  On the navigation pane, choose **Repositories**, and then choose **Create repository**\. 

1.  For **Repository name**, enter a name for your repository\.

1.  \(Optional\) In **Repository description**, enter an optional description for your repository\. 

1.  \(Optional\) In **Publish upstream repositories**, add intermediate repositories that connect your repositories to package authorities such as Maven Central or npmjs\.com\. 

1.  Choose **Next**\. 

1.  In **AWS account**, choose **This AWS account** if you are signed in to the account that owns the domain\. Choose **Different AWS account** if another AWS account owns the domain\.

1.  In **Domain**, choose the domain that the repository will be created in\.

    If there are no domains in the account, you must create one\. Enter the name for the new domain in **Domain name**\.

    Expand **Additional configuration**\. 

    You must use an AWS KMS key \(KMS key\) to encrypt all assets in your domain\. You can use an AWS managed key or a KMS key that you manage: 
**Important**  
CodeArtifact only supports [symmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)\. You cannot use an [asymmetric KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html#asymmetric-cmks) to encrypt your CodeArtifact domains\. For help determining whether a KMS key is symmetric or asymmetric, see [Identifying symmetric and asymmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/find-symm-asymm.html)\.
   +  Choose **AWS managed key** if you want to use the default AWS managed key\. 
   +  Choose **Customer managed key** if you want to use a KMS key that you manage\. To use a KMS key that you manage, in **Customer managed key ARN**, search for and choose the KMS key\. 

    For more information, see [AWS managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) and [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\. 

1.  Choose **Next**\. 

1.  In **Review and create**, review what CodeArtifact is creating for you\. 
   +  **Package flow** shows how your domain and repositories are connected\.
   +  **Step 1: Create repository** shows details about the repository and optional upstream repositories that will be created\. 
   +  **Step 2: Select domain** shows details about `my_domain`\. 

    When you're ready, choose **Create repository**\. 

## Create a repository \(AWS CLI\)<a name="create-repo-cli"></a>

Use the `create-repository` command to create a repository in your domain\.

```
aws codeartifact create-repository --domain my_domain --domain-owner 111122223333 --repository my_repo --description "My new repository"
```

Example output:

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012",
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:region-id:111122223333:repository/my_domain/my_repo",
        "description": "My new repository",
        "upstreams": "[]",
        "externalConnections"" "[]"
    }
}
```

A new repository doesn't contain any packages\. Each repository is associated with the AWS account that you're authenticated to when the repository is created\. An AWS account can have a maximum of 100 repositories\. Repositories that have been deleted with the `delete-repository` command don't count towards this limit\.

### Create a repository with tags<a name="create-repo-cli-tags"></a>

To create a repository with tags, add the `--tags` parameter to your `create-domain` command\.

```
aws codeartifact create-repository --domain my_domain --domain-owner 111122223333 --repository my_repo --tags key=k1,value=v1 key=k2,value=v2
```

## Create a repository with an upstream repository<a name="creating-a-repository-with-an-upstream"></a>

You can specify one or more upstream repositories when you create a repository\. 

```
aws codeartifact create-repository --domain my_domain --domain-owner 111122223333 --repository my_repo \
  --upstreams repositoryName=my-upstream-repo --repository-description "My new repository"
```

Example output:

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012",
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:region-id:111122223333:repository/my_domain/my_repo",
        "description": "My new repository",
        "upstreams": [
            {
                "repositoryName": "my-upstream-repo"
            }
        ],
        "externalConnections"" "[]"
    }
}
```

**Note**  
To create a repository with an upstream, you must have permission for the `AssociateWithDownstreamRepository` action on the upstream repository\.

To add an upstream to a repository after it's been created, see [Add or remove upstream repositories \(console\)](repo-upstream-add.md#repo-upstream-add-console) and [Add or remove upstream repositories \(AWS CLI\)](repo-upstream-add.md#repo-upstream-add-cli)\.