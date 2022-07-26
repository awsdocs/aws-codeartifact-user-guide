# Create VPC endpoints for CodeArtifact<a name="create-vpc-endpoints"></a>

 To create virtual private cloud \(VPC\) endpoints for CodeArtifact, use the Amazon EC2 `create-vpc-endpoint` AWS CLI command\. For more information, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon Virtual Private Cloud User Guide*\. 

 Two VPC endpoints are required so that all requests to CodeArtifact are in the AWS network\. The first endpoint is used to call CodeArtifact APIs \(for example, `GetAuthorizationToken` and `CreateRepository`\)\.

```
com.amazonaws.region.codeartifact.api
```

 The second endpoint is used to access CodeArtifact repositories using package managers and build tools \(for example, npm and Gradle\)\.

```
com.amazonaws.region.codeartifact.repositories
```

 The following command creates an endpoint to access CodeArtifact repositories\.

```
aws ec2 create-vpc-endpoint --vpc-id vpcid --vpc-endpoint-type Interface \
  --service-name com.amazonaws.region.codeartifact.api --subnet-ids subnetid \
  --security-group-ids groupid --no-private-dns-enabled
```

 The following command creates an endpoint to access package managers and build tools\.

```
aws ec2 create-vpc-endpoint --vpc-id vpcid --vpc-endpoint-type Interface \
  --service-name com.amazonaws.region.codeartifact.repositories --subnet-ids subnetid \
  --security-group-ids groupid --private-dns-enabled
```

**Note**  
 When you create a `codeartifact.repositories` endpoint, you must create a private DNS hostname using the `--private-dns-enabled` option\. However, because multiple private DNS hostnames are not currently supported for the `codeartifact.api` and `codeartifact.repositories` endpoints, use the `--no-private-dns-enabled` option for `codeartifact.api`\. If you can't or do not want to create a private DNS hostname when you create the `codeartifact.repositories` endpoint, you must follow an extra configuration step to use your package manager with CodeArtifact from a VPC\. See [Use the `codeartifact.repositories` endpoint without private DNS](use-codeartifact-from-vpc.md#use-codeartifact-from-vpc-no-private-dns) for more information\.

After creating VPC endpoints, you may need to do more configuration with security group rules to use the endpoints with CodeArtifact\. For more information about security groups in Amazon VPC, see [Security groups](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html#vpc-endpoints-security-groups)\.

If you are having issues connecting to CodeArtifact, you can use the VPC Reachability Analyzer tool to debug the issue\. For more information, see [What is VPC Reachability Analyzer?](https://docs.aws.amazon.com/vpc/latest/reachability/what-is-reachability-analyzer.html)