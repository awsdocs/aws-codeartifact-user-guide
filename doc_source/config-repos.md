# View or modify a repository configuration<a name="config-repos"></a>

 You can view and update details about your repository using the CodeArtifact console or the AWS Command Line Interface \(AWS CLI\)\. 

**Note**  
After you create a repository, you cannot change its name, associated AWS account, or domain\.

**Topics**
+ [View or modify a repository configuration \(console\)](#config-repos-console)
+ [View or modify a repository configuration \(AWS CLI\)](#config-repos-cli)

## View or modify a repository configuration \(console\)<a name="config-repos-console"></a>

 You can view details about and update your repository using the CodeArtifact console\. 

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1.  In the navigation pane, choose **Repositories**, and then choose the repository name that you want to view or modify\. 

1.  Expand **Details** to see the following: 
   +  The repository's domain\. Choose the domain name to learn more about it\. 
   +  The repository's resource policy\. Choose **Apply a repository policy** to add one\. 
   +  The repository's Amazon Resource Name \(ARN\)\. 
   +  If your repository has an external connection, you can choose the connection to learn more about it\. A repository can have only one external connection\. For more information, see [Add an external connection](external-connection.md)\. 
   +  If your repository has upstream repositories, you can choose one to see its details\. A repository can have up to 10 direct upstream repositories\. For more information, see [Working with upstream repositories in CodeArtifact](repos-upstream.md)\. 
**Note**  
 A repository can have an external connection or upstream repositories, but not both\. 

1.  In **Packages**, you can see any packages that are available to this repository\. Choose a package to learn more about it\. 

1.  Choose **View connection instructions**, and then choose a package manager to learn how to configure it with CodeArtifact\. 

1.  Choose **Apply repository policy** to update or add a resource policy to your repository\. For more information, see [ Repository policies](repo-policies.md)\. 

1.  Choose **Edit** to add or update the following\. 
   +  The repository description\. 
   +  Tags associated with the repository\.
   +  If your repository has an external connection, you can change which public repository it connects to\. Otherwise, you can add one or more existing repositories as upstream repositories\. Arrange them in the order you want them prioritized by CodeArtifact when a package is requested\. For more information, see [Upstream repository priority order](repo-upstream-search-order.md)\. 

## View or modify a repository configuration \(AWS CLI\)<a name="config-repos-cli"></a>

To view a repository's current configuration in CodeArtifact, use the `describe-repository` command\.

```
aws codeartifact describe-repository --domain my_domain --domain-owner 111122223333 --repository my_repo
```

Example output:

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012,
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:region-id:111122223333:repository/my_domain/my_repo"
        "upstreams": [],
        "externalConnections": []
    }
}
```

### Modify a repository upstream configuration<a name="modify-upstream"></a>

An upstream repository allows a package manager client to access the packages contained in more than one repository using a single URL endpoint\. To add or change a repository's upstream relationship, use the `update-repository` command\.

```
aws codeartifact update-repository --domain my_domain --domain-owner 111122223333 --repository my_repo \
       --upstreams repositoryName=my-upstream-repo
```

Example output:

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012,
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:region-id:111122223333:repository/my_domain/my_repo"
        "upstreams": [
            {
                "repositoryName": "my-upstream-repo"
            }
        ],
        "externalConnections": []
    }
}
```

**Note**  
To add an upstream repository, you must have permission for the `AssociateWithDownstreamRepository` action on the upstream repository\.

To remove a repository's upstream relationship, use an empty list as the argument to the `--upstreams` option\.

```
aws codeartifact update-repository --domain my_domain --domain-owner 111122223333 --repository my_repo --upstreams []
```

Example output:

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012,
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:region-id:111122223333:repository/my_domain/my_repo"
        "upstreams": [],
        "externalConnections": []
    }
}
```