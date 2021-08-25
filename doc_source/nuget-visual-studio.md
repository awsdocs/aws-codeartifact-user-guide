# Use CodeArtifact with Visual Studio<a name="nuget-visual-studio"></a>

 You can consume packages from CodeArtifact directly in Visual Studio with the CodeArtifact Credential Provider\. The credential provider simplifies the setup and authentication of your CodeArtifact repositories in Visual Studio and is available in the [AWS Toolkit for Visual Studio](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/welcome.html)\. 

**Note**  
 The AWS Toolkit for Visual Studio is not available for Visual Studio for Mac\. 

To configure and use NuGet with CLI tools, see [Use CodeArtifact with the nuget or dotnet CLI](nuget-cli.md)\.

**Topics**
+ [Configure Visual Studio with the CodeArtifact Credential Provider](#nuget-vs-cred-provider)
+ [Use the Visual Studio Package Manager console](#built-in-nuget-terminal-vs)

## Configure Visual Studio with the CodeArtifact Credential Provider<a name="nuget-vs-cred-provider"></a>

The CodeArtifact Credential Provider simplifies the setup and continued authentication between CodeArtifact and Visual Studio\. CodeArtifact authentication tokens are valid for a maximum of 12 hours\. To avoid having to manually refresh the token while working in Visual Studio, the credential provider periodically fetches a new token before the current token expires\.

**Important**  
To use the credential provider, ensure that any existing AWS CodeArtifact credentials are cleared from your `nuget.config` file that may have been added manually or by running `aws codeartifact login` to configure NuGet previously\.

**Use CodeArtifact in Visual Studio with the AWS Toolkit for Visual Studio**

1. Install the AWS Toolkit for Visual Studio using the following steps\. The toolkit is compatible with Visual Studio 2017 and 2019 using these steps\. AWS CodeArtifact does not support Visual Studio 2015 and earlier\.

   1.  The Toolkit for Visual Studio for Visual Studio 2017 and Visual Studio 2019 is distributed in the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AmazonWebServices.AWSToolkitforVisualStudio2017)\. You can also install and update the toolkit within Visual Studio by using **Tools** ≫ **Extensions and Updates** \(Visual Studio 2017\) or **Extensions** ≫ **Manage Extensions** \(Visual Studio 2019\)\. 

   1.  After the toolkit has been installed, open it by choosing **AWS Explorer** from the **View** menu\. 

1. Configure the Toolkit for Visual Studio with your AWS credentials by following the steps in [Providing AWS Credentials](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/credentials.html) in the *AWS Toolkit for Visual Studio User Guide*\.

1. \(Optional\) Set the AWS profile you want to use with CodeArtifact\. If not set, CodeArtifact will use the default profile\. To set the profile, go to **Tools > NuGet Package Manager > Select CodeArtifact AWS Profile**\.

1. Add your CodeArtifact repository as a package source in Visual Studio\.

   1. Navigate to your repository in the **AWS Explorer** window, right click and select `Copy NuGet Source Endpoint`\.

   1. Use the **Tools > Options** command and scroll to **NuGet Package Manager**\.

   1. Select the **Package Sources** node\.

   1. Select **\+**, edit the name, and paste the repository URL endpoint copied in Step 3a in the **Source** box, and select **Update**\.

   1. Select the checkbox for your newly added package source to enable it\.
**Note**  
We recommend adding an external connection to **NuGet\.org** to your CodeArtifact repository and disabling the **nuget\.org** package source in Visual Studio\. When using an external connection, all of the packages fetched from **NuGet\.org** will be stored in your CodeArtifact repository\. If **NuGet\.org** becomes unavailable, your application dependencies will still be available for CI builds and local development\. For more information about external connections, see [Add an external connection](external-connection.md)\.

After configuration, Visual Studio can consume packages from your CodeArtifact repository, any of its upstream repositories, or from [NuGet\.org](https://www.nuget.org/) if you have added an external connection\. For more information about browsing and installing NuGet packages in Visual Studio, see [Install and manage packages in Visual Studio using the NuGet Package Manager](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-visual-studio) in the *NuGet documentation*\.

## Use the Visual Studio Package Manager console<a name="built-in-nuget-terminal-vs"></a>

The Visual Studio Package Manager console will not use the Visual Studio version of the CodeArtifact Credential Provider\. To use it, you will have to configure the command\-line credential provider\. See [Use CodeArtifact with the nuget or dotnet CLI](nuget-cli.md) for more information\.