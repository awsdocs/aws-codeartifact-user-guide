# Tagging resources<a name="tag-resources"></a>

A *tag* is a custom attribute label that you or AWS assigns to an AWS resource\. Each AWS tag has two parts:
+ A *tag key* \(for example, `CostCenter`, `Environment`, `Project`, or `Secret`\)\. Tag keys are case sensitive\.
+ An optional field known as a *tag value* \(for example, `111122223333`, `Production`, or a team name\)\. Omitting the tag value is the same as using an empty string\. Like tag keys, tag values are case sensitive\.

Together these are known as key\-value pairs\.

Tags help you identify and organize your AWS resources\. Many AWS services support tagging, so you can assign the same tag to resources from different services to indicate that the resources are related\. For example, you can assign the same tag to a repository that you assign to an AWS CodeBuild project\.

For tips on using tags, see the [AWS Tagging Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) post on the *AWS Answers* blog\. 

You can tag the following resource types in CodeArtifact:
+ [Tag a repository in CodeArtifact](tag-repositories.md)
+ [Tag a domain in CodeArtifact](tag-domains.md)

You can use the console, AWS CLI, CodeArtifact APIs, or AWS SDKs to: 
+ Add tags to a domain or repository when you create it\*\.
+ Add, manage, and remove tags for a domain or repository\.

\* You cannot add tags to a domain or repository when you create it in the console\.

In addition to identifying, organizing, and tracking your resource with tags, you can use tags in IAM policies to help control who can view and interact with your resource\. For examples of tag\-based access policies, see [Using tags to control access to CodeArtifact resources](tag-based-access-control.md)