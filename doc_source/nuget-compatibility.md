# NuGet compatibility<a name="nuget-compatibility"></a>

 This guide contains information about CodeArtifact's compatibility with different NuGet tools and versions\. 

**Topics**
+ [General NuGet compatibility](#nuget-version-support)
+ [NuGet command line support](#nuget-command-line-support)

## General NuGet compatibility<a name="nuget-version-support"></a>

AWS CodeArtifact supports NuGet 4\.8 and higher\.

AWS CodeArtifact only supports V3 of the NuGet HTTP protocol\. This means that some CLI commands that rely V2 of the protocol are not supported\. See the [nuget\.exe command support](#nuget-command-support) section below for more information\.

AWS CodeArtifact does not support PowerShellGet 2\.x\.

## NuGet command line support<a name="nuget-command-line-support"></a>

AWS CodeArtifact supports the NuGet \(`nuget.exe`\) and \.NET Core \(`dotnet`\) CLI tools\.

### nuget\.exe command support<a name="nuget-command-support"></a>

Because CodeArtifact only supports V3 of NuGet's HTTP protocol, the following commands will not work when used against CodeArtifact resources:
+ `list`: The `nuget list` command displays a list of packages from a given source\. To get a list of packages in a CodeArtifact repository, you can use the [List package names](list-packages.md) command from the AWS CLI\.