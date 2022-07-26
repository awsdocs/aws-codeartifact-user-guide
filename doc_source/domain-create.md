# Create a domain<a name="domain-create"></a>

You can create a domain using the CodeArtifact console or the AWS Command Line Interface \(AWS CLI\)\. When you create a domain, it does not contain any repositories\. For more information, see [Create a repository](create-repo.md)\. 

**Topics**
+ [Create a domain \(console\)](#create-domain-console)
+ [Create a domain \(AWS CLI\)](#create-domain-cli)

## Create a domain \(console\)<a name="create-domain-console"></a>

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1.  In the navigation pane, choose **Domains**, and then choose **Create domain**\. 

1.  In **Name**, enter a name for your domain\. 

1.  Expand **Additional configuration**\. 

1.  You must use an AWS KMS key \(KMS key\) to encrypt all assets in your domain\. You can use an AWS managed KMS key or a KMS key that you manage\.
**Important**  
CodeArtifact only supports [symmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)\. You cannot use an [asymmetric KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html#asymmetric-cmks) to encrypt your CodeArtifact domains\. For help determining whether a KMS key is symmetric or asymmetric, see [Identifying symmetric and asymmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/find-symm-asymm.html)\.
   +  Choose **AWS managed key** if you want to use the default AWS managed key\. 
   +  Choose **Customer managed key** if you want to use a KMS key that you manage\. To use a KMS key that you manage, in **Customer managed key ARN**, search for and choose the KMS key\. 

    For more information, see [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) and [Customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\. 

1.  Choose **Create domain**\. 

## Create a domain \(AWS CLI\)<a name="create-domain-cli"></a>

Use the `create-domain` command to create domain with the AWS CLI\. You must use an AWS KMS key \(KMS key\) to encrypt all assets in your domain\. You can use an AWS managed KMS key or a KMS key that you manage\. If you use an AWS managed KMS key, do not use the `--encryption-key` parameter\. For more information about KMS keys, see [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) and [Customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\.

**Important**  
CodeArtifact only supports [symmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)\. You cannot use an [asymmetric KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html#asymmetric-cmks) to encrypt your CodeArtifact domains\. For help determining whether a KMS key is symmetric or asymmetric, see [Identifying symmetric and asymmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/find-symm-asymm.html)\.

```
aws codeartifact create-domain --domain my_domain
```

 JSON\-formatted data appears in the output with details about your new domain\. 

```
{
    "domain": {
        "name": "my_domain",
        "owner": "111122223333",
        "arn": "arn:aws:codeartifact:us-west-2:111122223333:domain/my_domain",
        "status": "Active",
        "encryptionKey": "arn:aws:kms:us-west-2:111122223333:key/your-kms-key",
        "repositoryCount": 0,
        "assetSizeBytes": 0,
        "createdTime": "2020-10-12T16:51:18.039000-04:00"
    }
}
```

 If you use a KMS key that you manage, include its Amazon Resource Name \(ARN\) with the `--encryption-key` parameter\. 

```
aws codeartifact create-domain --domain my_domain --encryption-key arn:aws:kms:us-west-2:111122223333:key/your-kms-key
```

 JSON\-formatted data appears in the output with details about your new domain\. 

```
{
    "domain": {
        "name": "my_domain",
        "owner": "111122223333",
        "arn": "arn:aws:codeartifact:us-west-2:111122223333:domain/my_domain",
        "status": "Active",
        "encryptionKey": "arn:aws:kms:us-west-2:111122223333:key/your-kms-key",
        "repositoryCount": 0,
        "assetSizeBytes": 0,
        "createdTime": "2020-10-12T16:51:18.039000-04:00"
    }
}
```

### Create a domain with tags<a name="create-domain-cli-tags"></a>

To create a domain with tags, add the `--tags` parameter to your `create-domain` command\.

```
aws codeartifact create-domain --domain my_domain --tags key=k1,value=v1 key=k2,value=v2
```