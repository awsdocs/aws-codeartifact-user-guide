# What is AWS CodeArtifact?<a name="welcome"></a>

 CodeArtifact is a fully managed artifact repository service that makes it easy for organizations to securely store and share software packages used for application development\. You can use CodeArtifact with popular build tools and package managers such as **NuGet**, **Maven**, **Gradle**, **npm**, **yarn**, **pip**, and **twine**\. 

 CodeArtifact automatically scales when you ingest or publish new packages to your repositories\. Because it's a fully managed service, the setup and operation of its infrastructure is done for you\. Integration with AWS Key Management Service \(AWS KMS\) secures all assets in a domain with one AWS KMS key \(KMS key\) that either you manage or AWS manages for you\. 

 For more information, see [AWS CodeArtifact](https://aws.amazon.com/codeartifact/)\.

## How does CodeArtifact work?<a name="codeartifact-how-does-it-work"></a>

CodeArtifact stores software packages in repositories\. Repositories are polyglot—a single repository can contain packages of any supported type\. Every CodeArtifact repository is a member of a single CodeArtifact domain\. We recommend that you use one production domain for your organization with one or more repositories\. For example, each repository might be used for a different development team\. Packages in your repositories can then be discovered and shared across your development teams\. 

To add packages to a repository, configure a package manager such as `npm` or `maven` to use the repository endpoint \(URL\)\. You can then use the package manager to publish packages to repository\. You can also import open\-source packages into a repository by configuring it with an external connection to a public repository such as npmjs, NuGet Gallery, Maven Central, or PyPI\. For more information, see [Connect a CodeArtifact repository to a public repository](external-connection.md)\. 

 You can make packages in one repository available to another repository in the same domain\. To do this, configure one repository as an upstream of the other\. All package versions available to the upstream repository are also available to the downstream repository\. In addition, all packages that are available to the upstream repository through an external connection to a public repository are available to the downstream repository\. For more information, see [Working with upstream repositories in CodeArtifact](repos-upstream.md)\. 

## How do I get started with CodeArtifact?<a name="how-do-i-get-started"></a>

 We recommend that you complete the following steps: 

1.  **Learn** more about CodeArtifact by reading the information in [AWS CodeArtifact Concepts](codeartifact-concepts.md)\. 

1.  **Set up** your AWS account, the AWS CLI, and an IAM user by following the steps in [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md)\. 

1.  **Use** CodeArtifact by following the instructions in [Getting started with CodeArtifact](getting-started.md)\. 