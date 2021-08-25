# Tag a domain in CodeArtifact<a name="tag-domains"></a>

Tags are key\-value pairs associated with AWS resources\. You can apply tags to your domains in CodeArtifact\. For information about CodeArtifact resource tagging, use cases, tag key and value constraints, and supported resource types, see [Tagging resources](tag-resources.md)\.

You can use the CLI to specify tags when you create a domain\. You can use the console or CLI to add or remove tags, and update the values of tags in a domain\. You can add up to 50 tags to each domain\.

**Topics**
+ [Tag domains \(CLI\)](#tag-domains-tag-cli)
+ [Tag domains \(console\)](#tag-domains-console)

## Tag domains \(CLI\)<a name="tag-domains-tag-cli"></a>

You can use the CLI to manage domain tags\.

**Topics**
+ [Add tags to a domain \(CLI\)](#add-domains-tag-cli)
+ [View tags for a domain \(CLI\)](#list-domains-tag-cli)
+ [Edit tags for a domain \(CLI\)](#update-domains-tag-cli)
+ [Remove tags from a domain \(CLI\)](#delete-domains-tag-cli)

### Add tags to a domain \(CLI\)<a name="add-domains-tag-cli"></a>

You can use the console or the AWS CLI to tag domains\.

To add a tag to a domain when you create it, see [Create a repository](create-repo.md)\.

In these steps, we assume that you have already installed a recent version of the AWS CLI or updated to the current version\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\.

At the terminal or command line, run the tag\-resource command, specifying the Amazon Resource Name \(ARN\) of the domain where you want to add tags and the key and value of the tag you want to add\.

**Note**  
To get the ARN of the domain, run the `describe-domain` command:  

```
aws codeartifact describe-domain --domain my_domain --query domain.arn
```

You can add more than one tag to a domain\. For example, to tag a domain named *my\_domain* with two tags, a tag key named *key1* with the tag value of *value1*, and a tag key named *key2* with the tag value of *value2*:

```
aws codeartifact tag-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:domain/my_domain --tags key=key1,value=value1 key=key2,value=value2
```

If successful, this command has no output\.

### View tags for a domain \(CLI\)<a name="list-domains-tag-cli"></a>

Follow these steps to use the AWS CLI to view the AWS tags for a domain\. If no tags have been added, the returned list is empty\.

At the terminal or command line, run the list\-tags\-for\-resource command with the Amazon Resource Name \(ARN\) of the domain\.

**Note**  
To get the ARN of the domain, run the `describe-domain` command:  

```
aws codeartifact describe-domain --domain my_domain --query domain.arn
```

For example, to view a list of tag keys and tag values for a domain named *my\_domain* with the `arn:aws:codeartifact:us-west-2:123456789012:domain/my_domain` ARN value:

```
aws codeartifact list-tags-for-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:domain/my_domain
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

### Edit tags for a domain \(CLI\)<a name="update-domains-tag-cli"></a>

Follow these steps to use the AWS CLI to edit a tag for a domain\. You can change the value for an existing key or add another key\. You can also remove tags from a domain, as shown in the next section\.

At the terminal or command line, run the tag\-resource command, specifying the ARN of the domain where you want to update a tag and specify the tag key and tag value:

**Note**  
To get the ARN of the domain, run the `describe-domain` command:  

```
aws codeartifact describe-domain --domain my_domain --query domain.arn
```

```
aws codeartifact tag-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:domain/my_domain --tags key=key1,value=newvalue1
```

If successful, this command has no output\.

### Remove tags from a domain \(CLI\)<a name="delete-domains-tag-cli"></a>

Follow these steps to use the AWS CLI to remove a tag from a domain\.

**Note**  
If you delete a domain, all tag associations are removed from the deleted domain\. You do not have to remove tags before you delete a domain\.

At the terminal or command line, run the untag\-resource command, specifying the ARN of the domain where you want to remove tags and the tag key of the tag you want to remove\.

**Note**  
To get the ARN of the domain, run the `describe-domain` command:  

```
aws codeartifact describe-domain --domain my_domain --query domain.arn
```

For example, to remove multiple tags on a domain named *mydomain* with the tag keys *key1* and *key2*:

```
aws codeartifact untag-resource --resource-arn arn:aws:codeartifact:us-west-2:123456789012:domain/my_domain --tag-keys key1 key2
```

If successful, this command has no output\. After removing tags, you can view the remaining tags on the repository using the `list-tags-for-resource` command\.

## Tag domains \(console\)<a name="tag-domains-console"></a>

You can use the console or the CLI to tag resources\.

**Topics**
+ [Add tags to a domain \(console\)](#add-tag-domains-console)
+ [View tags for a domain \(console\)](#list-tag-domains-console)
+ [Edit tags for a domain \(console\)](#update-tag-domains-console)
+ [Remove tags from a domain \(console\)](#delete-tag-domains-console)

### Add tags to a domain \(console\)<a name="add-tag-domains-console"></a>

You can use the console to add tags to an existing domain\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Domains** page, choose the domain that you want to add tags to\.

1. Expand the **Details** section\.

1. Under **Domain tags**, choose **Add domain tags** if there are no tags on the domain, or choose **View and edit domain tags** if there are\.

1. Choose **Add new tag**\.

1. In the **Key** and **Value** fields, enter the text for each tag you want to add\. \(The **Value** field is optional\.\) For example, in **Key**, enter **Name**\. In **Value**, enter **Test**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/codeartifact/latest/ug/)

1. \(Optional\) Choose **Add tag** to add more rows and enter more tags\.

1. Choose **Update domain**\.

### View tags for a domain \(console\)<a name="list-tag-domains-console"></a>

You can use the console to list tags for existing domains\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Domains** page, choose the domain where you want to view tags\.

1. Expand the **Details** section\.

1. Under **Domain tags**, choose **View and edit domain tags**\.
**Note**  
If there are no tags added to this domain, the console will read **Add domain tags**\.

### Edit tags for a domain \(console\)<a name="update-tag-domains-console"></a>

You can use the console to edit tags that have been added to domain\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Domains** page, choose the domain where you want to update tags\.

1. Expand the **Details** section\.

1. Under **Domain tags**, choose **View and edit domain tags**\.
**Note**  
If there are no tags added to this domain, the console will read **Add domain tags**\.

1. In the **Key** and **Value** fields, update the values in each field as needed\. For example, for the **Name** key, in **Value**, change **Test** to **Prod**\.

1. Choose **Update domain**\.

### Remove tags from a domain \(console\)<a name="delete-tag-domains-console"></a>

You can use the console to delete tags from domains\.

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. On the **Domains** page, choose the domain where you want to remove tags\.

1. Expand the **Details** section\.

1. Under **Domain tags**, choose **View and edit domain tags**\.
**Note**  
If there are no tags added to this domain, the console will read **Add domain tags**\.

1. Next to the key and value for each tag you want to delete, choose **Remove**\.

1. Choose **Update domain**\.