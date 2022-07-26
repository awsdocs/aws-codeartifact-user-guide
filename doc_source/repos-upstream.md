# Working with upstream repositories in CodeArtifact<a name="repos-upstream"></a>

A repository can have other AWS CodeArtifact repositories as *upstream* repositories\. This enables a package manager client to access the packages that are contained in more than one repository using a single repository endpoint\.

You can add one or more upstream repositories to an AWS CodeArtifact repository using the AWS Management Console, AWS CLI, or SDK\. To associate a repository with an upstream repository, you must have permission for the `AssociateWithDownstreamRepository` action on the upstream repository\. For more information, see [Create a repository with an upstream repository](create-repo.md#creating-a-repository-with-an-upstream) and [Add or remove upstream repositories](repo-upstream-add.md)\. 

If an upstream repository has an external connection to a public repository, the repositories that are downstream from it can pull packages from that public repository\. For example, suppose that the repository `my_repo` has an upstream repository named `upstream`, and `upstream` has an external connection to a public npm repository\. In this case, a package manager that is connected to `my_repo` can pull packages from the npm public repository\. For more information about requesting packages from upstream repositories or external connections, see [Requesting a package version with upstream repositories](repo-upstream-behavior.md) or [Requesting packages from external connections](external-connection-requesting-packages.md)\.

**Topics**
+ [What's the difference between upstream repositories and external connections?](#repos-external-connections)
+ [Add or remove upstream repositories](repo-upstream-add.md)
+ [Connect a CodeArtifact repository to a public repository](external-connection.md)
+ [Requesting a package version with upstream repositories](repo-upstream-behavior.md)
+ [Requesting packages from external connections](external-connection-requesting-packages.md)
+ [Upstream repository priority order](repo-upstream-search-order.md)
+ [API behavior with upstream repositories](upstream-repo-api-behavior.md)

## What's the difference between upstream repositories and external connections?<a name="repos-external-connections"></a>

In CodeArtifact, upstream repositories and external connections behave mostly the same, but there are a few important differences\.

1. You can add up to 10 upstream repositories to a CodeArtifact repository\. You can only add one external connection\.

1. There are separate API calls to add an upstream repository or an external connection\.

1. The package retention behavior is slightly different, as packages requested from upstream repositories are retained in those repositories\. For more information, see [Package retention in intermediate repositories](repo-upstream-behavior.md#package-retention-intermediate-repositories)\.