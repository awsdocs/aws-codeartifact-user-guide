# Add, update, or remove upstream repositories \(AWS CLI\)<a name="repo-upstream-add-cli"></a>

 You can add, update, or remove a CodeArtifact repository's upstream repositories using the AWS Command Line Interface \(AWS CLI\)\. To do this, use the `update-repository` command, and specify the upstream repositories using the `--upstreams` parameter\. 

**Note**  
This guide contains information about configuring other CodeArtifact repositories as upstream repositories\. For information about configuring an external connection to public repositories like npmjs\.com, Maven Central, or PyPI, see [Add an external connection](https://docs.aws.amazon.com/codeartifact/latest/ug/external-connection.html)\.

 The following command adds two upstream repositories to a repository named `my_repo` that is in a domain named `my_domain`\. The order of the upstream repositories in the `--upstreams` parameter determines their search priority when CodeArtifact requests a package from the `my_repo` repository\. For more information, see [Upstream repository priority order](repo-upstream-search-order.md)\. 

```
aws codeartifact update-repository --repository my_repo --domain my_domain --domain-owner 111122223333 --upstreams repositoryName=upstream-1 repositoryName=upstream-2
```

 The output contains the upstream repositories, as follows\.

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012",
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:us-east-2:111122223333:repository/my_domain/my_repo",
        "upstreams": [
            {
                "repositoryName": "upstream-1"
            },
            {
                "repositoryName": "upstream-2"
            }
        ],
        "externalConnections": []
    }
}
```

 To remove all upstream repositories from a repository, use the `update-repository` command and include `--upstreams` without an argument\. The following removes upstream repositories from a repository named `my_repo` that is contained in a domain named `my_domain`\.

```
aws codeartifact update-repository --repository my_repo --domain my_domain --domain-owner 111122223333 --upstreams
```

 The output shows that the list of `upstreams` is empty\.

```
{
    "repository": {
        "name": "my_repo",
        "administratorAccount": "123456789012",
        "domainName": "my_domain",
        "domainOwner": "111122223333",
        "arn": "arn:aws:codeartifact:us-east-2:111122223333:repository/my_domain/my_repo",
        "upstreams": [],
        "externalConnections": []
    }
}
```