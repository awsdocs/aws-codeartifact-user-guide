# Add or remove upstream repositories<a name="repo-upstream-add"></a>

Follow the steps in the following sections to add or remove upstream repositories to or from an CodeArtifact repository\. For more information about upstream repositories, see [Working with upstream repositories in CodeArtifact](repos-upstream.md)\.

This guide contains information about configuring other CodeArtifact repositories as upstream repositories\. For information about configuring an external connection to public repositories like npmjs\.com, Nuget Gallery, Maven Central, or PyPI, see [Add an external connection](https://docs.aws.amazon.com/codeartifact/latest/ug/external-connection.html)\.

## Add or remove upstream repositories \(console\)<a name="repo-upstream-add-console"></a>

Perform the steps in the following procedure to add a repository as an upstream repository using the CodeArtifact console\. For information about adding an upstream repository with the AWS CLI, see [Add or remove upstream repositories \(AWS CLI\)](#repo-upstream-add-cli)\.

**To add an upstream repository using the CodeArtifact console**

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1.  In the navigation pane, choose **Domains**, and then choose the domain name that contains your repository\. 

1.  Choose the name of your repository\.

1.  Choose **Edit**\.

1.  In **Upstream repositories**, choose **Associate upstream repository** and add the repository you want to add as an upstream repository\. You can only add repositories in the same domain as upstream repositories\.

1.  Choose **Update repository**\.

**To remove an upstream repository using the CodeArtifact console**

1. Open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/home](https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1.  In the navigation pane, choose **Domains**, and then choose the domain name that contains your repository\. 

1.  Choose the name of your repository\.

1.  Choose **Edit**\.

1.  In **Upstream repositories**, find the list entry of the upstream repository you want to remove and choose **Disassociate**\.
**Important**  
Once you remove an upstream repository from a CodeArtifact repository, package managers will not have access to packages in the upstream repository or any of its upstream repositories\.

1.  Choose **Update repository**\.

## Add or remove upstream repositories \(AWS CLI\)<a name="repo-upstream-add-cli"></a>

 You can add or remove a CodeArtifact repository's upstream repositories using the AWS Command Line Interface \(AWS CLI\)\. To do this, use the `update-repository` command, and specify the upstream repositories using the `--upstreams` parameter\. 

You can only add repositories in the same domain as upstream repositories\.

**To add upstream repositories \(AWS CLI\)**

1. If you haven't, follow the steps in [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md) to set up and configure the AWS CLI with CodeArtifact\.

1. Use the `aws codeartifact update-repository` command with the `--upstreams` flag to add upstream repositories\.
**Note**  
Calling the `update-repository` command replaces the existing configured upstream repositories with the list of repositories provided with the `--upstreams` flag\. If you want to add upstream repositories and keep the existing ones, you must include the existing upstream repositories in the call\.

   The following example command adds two upstream repositories to a repository named `my_repo` that is in a domain named `my_domain`\. The order of the upstream repositories in the `--upstreams` parameter determines their search priority when CodeArtifact requests a package from the `my_repo` repository\. For more information, see [Upstream repository priority order](repo-upstream-search-order.md)\. 

   ```
   aws codeartifact update-repository --repository my_repo --domain my_domain --domain-owner 111122223333 \
    --upstreams repositoryName=upstream-1 repositoryName=upstream-2
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

**To remove an upstream repository \(AWS CLI\)**

1. If you haven't, follow the steps in [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md) to set up and configure the AWS CLI with CodeArtifact\.

1. To remove upstream repositories from a CodeArtifact repository, use the `update-repository` command with the `--upstreams` flag\. The list of repositories provided to the command will be the new set of upstream repositories for the CodeArtifact repository\. Include existing upstream repositories that you want to keep, and omit the upstream repositories you want to remove\.

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