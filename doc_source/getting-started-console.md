# Getting started using the console<a name="getting-started-console"></a>

 Run the following steps to get started with CodeArtifact using the AWS Management Console\. 

1. Sign in to the AWS Management Console and open the AWS CodeArtifact console at [https://console\.aws\.amazon\.com/codesuite/codeartifact/start](https://console.aws.amazon.com/codesuite/codeartifact/start)\. For more information, see [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md)\.

1.  Choose **Create repository**\. 

1.  In **Repository name**, enter **my\-repo**\. 

1.  \(Optional\) In **Repository Description**, enter an optional description for your repository\.

1.  In **Public upstream repositories**, select **npm\-store** to create a repository connected to **npmjs** that is upstream from your `my-repo` repository\.

   CodeArtifact assigns the name `npm-store` to this repository for you\. All packages available in the upstream repository `npm-store` are also available to its downstream repository, `my-repo`\. 

1.  Choose **Next**\. 

1.  In **AWS account**, choose **This AWS account**\.

1.  In **Domain name**, enter **my\-domain**\. 

1.  Expand **Additional configuration**\. 

1.  You must use a *customer master key* \(CMK\) to encrypt all assets in your domain\. You can use an AWS managed CMK or a CMK that you manage: 
   +  Choose **AWS managed key** if you want to use the default AWS managed CMK\. 
   +  Choose **Customer managed key** if you want to use a CMK that you manage\. If you use a CMK that you manage, in **Customer master key**, choose the CMK\. 

    For more information, see [AWS managed CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) and [Customer managed CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\. 

1.  Choose **Next**\. 

1.  In **Review and create**, review what CodeArtifact is creating for you\. 
   +  **Package flow** shows how `my-domain`, `my-repo`, and `npm-store` are related\. 
   +  **Step 1: Create repository** shows details about `my-repository` and `npm-store`\. 
   +  **Step 2: Select domain** shows details about `my-domain`\. 

    When you're ready, choose **Create repository**\. 

1.  On the **my\-repo** page, choose **View connection instructions**, and then choose **npm**\. 

1.  Use the AWS CLI to run the `login` command shown under **Configure your npm client using this AWS CLI CodeArtifact command**\.

   ```
   aws codeartifact login --tool npm --repository my-repo --domain my-domain --domain-owner your-AWS-account-ID
   ```

You should receive output confirming your login succeeded\.
   ```
   Successfully configured npm to use AWS CodeArtifact repository https://my-domain-8529020723677.d.codeartifact.us-east-2.amazonaws.com/npm/my-repo/
   Login expires in 12 hours at 2020-10-08 02:45:33-04:00
   ```

If you receive the error `Could not connect to the endpoint URL`, make sure that your AWS CLI is configured and that your **Default region name** is set to the same region where you created your artifact repository, see [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

For more information, see [Authentication with npm](npm-auth.md)\.

1.  Use the npm CLI to install an npm library\. For example, install a library listed in [https://www\.npmjs\.com/](https://www.npmjs.com/)\.
**Note**  
*lodash* is a popular package to use\.

   ```
   npm install library-name
   ```

1.  Return to the CodeArtifact console\. If your **my\-repo** repository is open, refresh the page\. Otherwise, in the navigation pane, choose **Repositories**, and then choose **my\-repo**\. 

    Under **Packages**, you should see the npm library, or package, that you installed\. You can choose the name of the package to view its version and status\. You can choose its latest version to view package details such as dependencies, assets, and more\.
**Note**  
 There may be a delay between when you install the package and when it is ingested into your repository\. 

1.  To avoid further AWS charges, delete the resources that you used during this tutorial: 
**Note**
 You cannot delete a domain that contains repositories, so you must delete `my-repo` and `npm-store` before you delete `my-domain`\.

   1.  In the navigation pane, choose **Repositories**\.

   1.  Choose **npm\-store**, choose **Delete**, and then follow the steps to delete the repository\. 

   1.  Choose **my\-repo**, choose **Delete**, and then follow the steps to delete the repository\. 

   1.  In the navigation pane, choose **Domains**\.

   1.  Choose **my\-domain**, choose **Delete**, and then follow the steps to delete the domain\. 
