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

In addition to identifying, organizing, and tracking your resource with tags, you can use tags in IAM policies to help control who can view and interact with your resource\. For examples of tag\-based access policies, see [Using tags to control access to CodeArtifact resources](tag-based-access-control.md)\.

## CodeArtifact cost allocation with tags<a name="tag-resources-cost-allocation"></a>

You can use tags to allocate both storage and request costs in CodeArtifact\.

### Allocating data storage costs in CodeArtifact<a name="tag-resources-cost-allocation-storage"></a>

Data storage costs are tied to domains, therefore to allocate your CodeArtifact storage costs, you can use any tags that are applied to your domains\. For information about adding tags to domains, see [Tag a domain in CodeArtifact](tag-domains.md)\.

### Allocating request costs in CodeArtifact<a name="tag-resources-cost-allocation-requests"></a>

Most request usage is tied to repositories, therefore to allocate your CodeArtifact requests costs, you can use any tags that are applied to your repositories\. For information about adding tags to repositories, see [Tag a repository in CodeArtifact](tag-repositories.md)\.

Some request types are associated with domains rather than repositories, so the request usage and costs related to the requests will be allocated to the tags on the domain\. The best way to determine if a request type is associated with a domain or a repository is to use the [Actions defined by AWS CodeArtifact](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awscodeartifact.html#awscodeartifact-actions-as-permissions) table in the *Service Authorization Reference*\. Find the request type in the **Actions** column, and look at the value in the corresponding **Resources types** column\. If the resource type is **domain**, requests of that type will be billed to the domain\. If the resource type is **repository** or **package**, requests of that type will be billed to the repository\. Some actions show both resource types, for those actions the billed resource depends on what value is passed in the request\.