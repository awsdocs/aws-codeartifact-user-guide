# Getting started using the AWS CLI<a name="getting-started-cli"></a>

 Run the following steps to get started with CodeArtifact using the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Install or upgrade and then configure the AWS CLI](get-set-up-install-cli.md)\. This guide uses the `npm` package manager, if you are using a different package manager, you will need to modify some of the following steps\.

1.  Use the AWS CLI to run the create\-domain command\. 

   ```
   aws codeartifact create-domain --domain my-domain
   ```

    JSON\-formatted data appears in the output with details about your new domain\. 

   ```
   {
       "domain": {
           "name": "my-domain",
           "owner": "111122223333",
           "arn": "arn:aws:codeartifact:us-west-2:111122223333:domain/my-domain",
           "status": "Active",
           "createdTime": "2020-10-07T15:36:35.194000-04:00",
           "encryptionKey": "arn:aws:kms:us-west-2:111122223333:key/your-kms-key",
           "repositoryCount": 0,
           "assetSizeBytes": 0
       }
   }
   ```

   If you receive the error `Could not connect to the endpoint URL`, make sure that your AWS CLI is configured and that your **Default region name** is set to the same Region where you created your repository, see [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

1.  Use the create\-repository command to create a repository in your domain\.

   ```
   aws codeartifact create-repository --domain my-domain --domain-owner 111122223333 --repository my-repo
   ```

    JSON\-formatted data appears in the output with details about your new repository\. 

   ```
   {
       "repository": {
           "name": "my-repo",
           "administratorAccount": "111122223333",
           "domainName": "my-domain",
           "domainOwner": "111122223333",
           "arn": "arn:aws:codeartifact:us-west-2:111122223333:repository/my-domain/my-repo",
           "upstreams": [],
           "externalConnections": []
       }
   }
   ```

1. Use the create\-repository command to create an upstream repository for your `my-repo` repository\.

   ```
   aws codeartifact create-repository --domain my-domain --domain-owner 111122223333 --repository npm-store
   ```

    JSON\-formatted data appears in the output with details about your new repository\.

   ```
   {
       "repository": {
           "name": "npm-store",
           "administratorAccount": "111122223333",
           "domainName": "my-domain",
           "domainOwner": "111122223333",
           "arn": "arn:aws:codeartifact:us-west-2:111122223333:repository/my-domain/npm-store",
           "upstreams": [],
           "externalConnections": []
       }
   }
   ```

1.  Use the associate\-external\-connection command to add an external connection to the npm public repository to your `npm-store` repository\. 

   ```
   aws codeartifact associate-external-connection --domain my-domain --domain-owner 111122223333 --repository npm-store --external-connection "public:npmjs"
   ```

    JSON\-formatted data appears in the output with details about the repository and its new external connection\.

   ```
   {
       "repository": {
           "name": "npm-store",
           "administratorAccount": "111122223333",
           "domainName": "my-domain",
           "domainOwner": "111122223333",
           "arn": "arn:aws:codeartifact:us-west-2:111122223333:repository/my-domain/npm-store",
           "upstreams": [],
           "externalConnections": [
               {
                   "externalConnectionName": "public:npmjs",
                   "packageFormat": "npm",
                   "status": "AVAILABLE"
               }
           ]
       }
   }
   ```

    For more information, see [Connect a CodeArtifact repository to a public repository](external-connection.md)\. 

1.  Use the update\-repository command to associate the `npm-store` repository as an upstream repository to the `my-repo` repository\. 

   ```
   aws codeartifact update-repository --repository my-repo --domain my-domain --domain-owner 111122223333 --upstreams repositoryName=npm-store
   ```

    JSON\-formatted data appears in the output with details about your updated repository, including its new upstream repository\.

   ```
   {
       "repository": {
           "name": "my-repo",
           "administratorAccount": "111122223333",
           "domainName": "my-domain",
           "domainOwner": "111122223333",
           "arn": "arn:aws:codeartifact:us-west-2:111122223333:repository/my-domain/my-repo",
           "upstreams": [
               {
                   "repositoryName": "npm-store"
               }
           ],
           "externalConnections": []
       }
   }
   ```

    For more information, see [Add or remove upstream repositories \(AWS CLI\)](repo-upstream-add.md#repo-upstream-add-cli)\. 

1. Use the login command to configure your npm package manager with your `my-repo` repository\. 

   ```
   aws codeartifact login --tool npm --repository my-repo --domain my-domain --domain-owner 111122223333
   ```

   You should receive output confirming your login succeeded\.

   ```
   Successfully configured npm to use AWS CodeArtifact repository https://my-domain-111122223333.d.codeartifact.us-east-2.amazonaws.com/npm/my-repo/
   Login expires in 12 hours at 2020-10-08 02:45:33-04:00
   ```

    For more information, see [Configure and use npm with CodeArtifact](npm-auth.md)\. 

1.  Use the npm CLI to install an npm package\. For example, to install the popular npm package `lodash`, use the following command\. 

   ```
   npm install lodash
   ```

1.  Use the list\-packages command to view the package you just installed in your `my-repo` repository\. 
**Note**  
There may be a delay between when the `npm install` install command completes and when the package is visible in your repository\. For details on typical latency when fetching packages from public repositories, see [External connection latency](external-connection-requesting-packages.md#external-connection-latency)\. 

   ```
   aws codeartifact list-packages --domain my-domain --repository my-repo
   ```

    JSON\-formatted data appears in the output with the format and name of the package that you installed\. 

   ```
   {
       "packages": [
           {
               "format": "npm",
               "package": "lodash"
           }
       ]
   }
   ```

   You now have three CodeArtifact resources: 
   +  The domain `my-domain`\. 
   +  The repository `my-repo` that is contained in `my-domain`\. This repository has an npm package available to it\. 
   +  The repository `npm-store` that is contained in `my-domain`\. This repository has an external connection to the public npm repository and is associated as an upstream repository with the `my-repo` repository\. 

1. To avoid further AWS charges, delete the resources that you used during this tutorial: 
**Note**  
 You cannot delete a domain that contains repositories, so you must delete `my-repo` and `npm-store` before you delete `my-domain`\. 

   1.  Use the delete\-repository command to delete the `npm-store` repository\. 

      ```
      aws codeartifact delete-repository --domain my-domain --domain-owner 111122223333 --repository my-repo
      ```

       JSON\-formatted data appears in the output with details about the deleted repository\. 

      ```
      {
          "repository": {
              "name": "my-repo",
              "administratorAccount": "111122223333",
              "domainName": "my-domain",
              "domainOwner": "111122223333",
              "arn": "arn:aws:codeartifact:us-west-2:111122223333:repository/my-domain/my-repo",
              "upstreams": [
                  {
                      "repositoryName": "npm-store"
                  }
              ],
              "externalConnections": []
          }
      }
      ```

   1.  Use the delete\-repository command to delete the `npm-store` repository\. 

      ```
      aws codeartifact delete-repository --domain my-domain --domain-owner 111122223333 --repository npm-store
      ```

       JSON\-formatted data appears in the output with details about the deleted repository\.

      ```
      {
          "repository": {
              "name": "npm-store",
              "administratorAccount": "111122223333",
              "domainName": "my-domain",
              "domainOwner": "111122223333",
              "arn": "arn:aws:codeartifact:us-west-2:111122223333:repository/my-domain/npm-store",
              "upstreams": [],
              "externalConnections": [
                  {
                      "externalConnectionName": "public:npmjs",
                      "packageFormat": "npm",
                      "status": "AVAILABLE"
                  }
              ]
          }
      }
      ```

   1. Use the delete\-domain command to delete the `my-domain` repository\. 

      ```
      aws codeartifact delete-domain --domain my-domain --domain-owner 111122223333
      ```

       JSON\-formatted data appears in the output with details about the deleted domain\.

      ```
      {
          "domain": {
              "name": "my-domain",
              "owner": "111122223333",
              "arn": "arn:aws:codeartifact:us-west-2:111122223333:domain/my-domain",
              "status": "Deleted",
              "createdTime": "2020-10-07T15:36:35.194000-04:00",
              "encryptionKey": "arn:aws:kms:us-west-2:111122223333:key/your-kms-key",
              "repositoryCount": 0,
              "assetSizeBytes": 0
          }
      }
      ```