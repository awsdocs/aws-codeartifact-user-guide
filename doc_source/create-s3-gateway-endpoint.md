# Create the Amazon S3 gateway endpoint<a name="create-s3-gateway-endpoint"></a>

CodeArtifact uses Amazon Simple Storage Service \(Amazon S3\) to store package assets\. To pull packages from CodeArtifact, you must create a gateway endpoint for Amazon S3\. When your build or deployment process downloads packages from CodeArtifact, it must access CodeArtifact to get package metadata and Amazon S3 to download package assets \(for example, Maven `.jar` files\)\. 

 To create the Amazon S3 gateway endpoint for CodeArtifact, use the Amazon EC2 `create-vpc-endpoint` AWS CLI command\. When you create the endpoint, you must select the route tables for your VPC\. For more information, see [Gateway VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html) in the *Amazon Virtual Private Cloud User Guide*\. 

The following command creates an Amazon S3 endpoint\.

```
aws ec2 create-vpc-endpoint --vpc-id vpcid --service-name com.amazonaws.region.s3 \
  --route-table-ids routetableid
```