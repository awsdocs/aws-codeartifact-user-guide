# Tag a repository in CodeArtifact<a name="tag-repositories"></a>

Tags are key\-value pairs associated with AWS resources\. You can apply tags to your repositories in CodeArtifact\. For information about CodeArtifact resource tagging, use cases, tag key and value constraints, and supported resource types, see [Tagging resources](tag-resources.md)\.

You can use the CLI to specify tags when you create a repository\. You can use the console or CLI to add or remove tags, and update the values of tags in a repository\. You can add up to 50 tags to each repository\.

**Topics**
+ [Tag repositories \(CLI\)](#tag-repositories-tag-cli)
+ [Tag repositories \(console\)](#tag-repositories-console)

## Tag repositories \(CLI\)<a name="tag-repositories-tag-cli"></a>

You can use the CLI to manage repository tags\.

**Topics**
+ [Add tags to a repository \(CLI\)](#add-repositories-tag-cli)
+ [View tags for a repository \(CLI\)](#list-repositories-tag-cli)
+ [Edit tags for a repository \(CLI\)](#update-repositories-tag-cli)
+ [Remove tags from a repository \(CLI\)](#delete-repositories-tag-cli)

### Add tags to a repository \(CLI\)<a name="add-repositories-tag-cli"></a>

You can use the console or the AWS CLI to tag repositories\.

To add a tag to a repository when you create it, see [Create a repository](create-repo.md)\.

In these steps, we assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the repository where you want to add tags and the key and value of the tag you want to add\.

**Note**  
To get the ARN of the repository, run the `describe-repository` command:  

```
aws codeartifact describe-repository --domain my-domain --repository my-repo --query repository.arn
```

You can add more than one tag to a repository\. For example, to tag a repository named *my\-repo* in a domain named *my\-domain* with two tags, a tag key named *key1* with the tag value of *value1*, and a tag key named *key2* with the tag value of *value2*:

```
aws codeartifact tag-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:repository/my-domain/my-repo --tags key=key1,value=value1 key=key2,value=value2
```

If successful, this command has no output\.

### View tags for a repository \(CLI\)<a name="list-repositories-tag-cli"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a repository\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command\.

**Note**  
To get the ARN of the repository, run the `describe-repository` command:  

```
aws codeartifact describe-repository --domain my-domain --repository my-repo --query repository.arn
```

For example, to view a list of tag keys and tag values for a repository named *my\-repo* in a domain named *my\-domain* with the `arn:aws:codeartifact:us-west-2:123456789012:repository/my-domain/my-repo` ARN value:

```
aws codeartifact list-tags-for-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:repository/my-domain/my-repo
```

If successful, this command returns information similar to the following:

```
{
    "tags": {
        "key1": "value1",
        "key2": "value2"
    }
}
```

### Edit tags for a repository \(CLI\)<a name="update-repositories-tag-cli"></a>

Follow these steps to use the AWS CLI to edit a tag for a repository\. You can change the value for an existing key or add another key\.

At the terminal or command line, run the tag\-resource command, specifying the ARN of the repository where you want to update a tag and specify the tag key and tag value\.

**Note**  
To get the ARN of the repository, run the `describe-repository` command:  

```
aws codeartifact describe-repository --domain my-domain --repository my-repo --query repository.arn
```

```
aws codeartifact tag-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:repository/my-domain/my-repo --tags key=key1,value=newvalue1
```

If successful, this command has no output\.

### Remove tags from a repository \(CLI\)<a name="delete-repositories-tag-cli"></a>

Follow these steps to use the AWS CLI to remove a tag from a repository\.

**Note**  
If you delete a repository, all tag associations are removed from the deleted repository\. You do not have to remove tags before you delete a repository\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the repository where you want to remove tags and the tag key of the tag you want to remove\.

**Note**  
To get the ARN of the repository, run the `describe-repository` command:  

```
aws codeartifact describe-repository --domain my-domain --repository my-repo --query repository.arn
```

For example, to remove multiple tags on a repository named *my\-repo* in a domain named *my\-domain* with the tag keys *key1* and *key2*:

```
aws codeartifact untag-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:repository/my-domain/my-repo --tag-keys key1 key2
```

If successful, this command has no output\. After removing tags, you can view the remaining tags on the repository using the `list-tags-for-resource` command\.

## Tag repositories \(console\)<a name="tag-repositories-console"></a>

You can use the console or the CLI to tag resources\.

**Topics**
+ [Add tags to a repository \(console\)](#add-tag-repositories-console)
+ [View tags for a repository \(console\)](#list-tag-repositories-console)
+ [Edit tags for a repository \(console\)](#update-tag-repositories-console)
+ [Remove tags from a repository \(console\)](#delete-tag-repositories-console)

### Add tags to a repository \(console\)<a name="add-tag-repositories-console"></a>

You can use the console to add tags to an existing repository\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Repositories** page, choose the repository that you want to add tags to\.

1. Expand the **Details** section\.

1. Under **Repository tags**, if there are no tags on the repository, choose **Add repository tags**\. If there are tags on the repository, choose **View and edit repository tags**\.

1. Choose **Add new tag**\.

1. In the **Key** and **Value** fields, enter the text for each tag you want to add\. \(The **Value** field is optional\.\) For example, in **Key**, enter **Name**\. In **Value**, enter **Test**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

1. \(Optional\) Choose **Add tag** to add more rows and enter more tags\.

1. Choose **Update repository**\.

### View tags for a repository \(console\)<a name="list-tag-repositories-console"></a>

You can use the console to list tags for existing repositories\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Repositories** page, choose the repository where you want to view tags\.

1. Expand the **Details** section\.

1. Under **Repository tags**, choose **View and edit repository tags**\.
**Note**  
If there are no tags added to this repository, the console will read **Add repository tags**\.

### Edit tags for a repository \(console\)<a name="update-tag-repositories-console"></a>

You can use the console to edit tags that have been added to repository\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Repositories** page, choose the repository where you want to update tags\.

1. Expand the **Details** section\.

1. Under **Repository tags**, choose **View and edit repository tags**\.
**Note**  
If there are no tags added to this repository, the console will read **Add repository tags**\.

1. In the **Key** and **Value** fields, update the values in each field as needed\. For example, for the **Name** key, in **Value**, change **Test** to **Prod**\.

1. Choose **Update repository**\.

### Remove tags from a repository \(console\)<a name="delete-tag-repositories-console"></a>

You can use the console to delete tags from repositories\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Repositories** page, choose the repository where you want to remove tags\.

1. Expand the **Details** section\.

1. Under **Repository tags**, choose **View and edit repository tags**\.
**Note**  
If there are no tags added to this repository, the console will read **Add repository tags**\.

1. Next to the key and value for each tag you want to delete, choose **Remove**\.

1. Choose **Update repository**\.