# Create the Amazon S3 gateway endpoint<a name="create-s3-gateway-endpoint"></a>

CodeArtifact uses Amazon Simple Storage Service \(Amazon S3\) to store package assets\. To pull packages from CodeArtifact, you must create a gateway endpoint for Amazon S3\. When your build or deployment process downloads packages from CodeArtifact, it must access CodeArtifact to get package metadata and Amazon S3 to download package assets \(for example, Maven `.jar` files\)\. 

 To create the Amazon S3 gateway endpoint for CodeArtifact, use the Amazon EC2 `create-vpc-endpoint` AWS CLI command\. When you create the endpoint, you must select the route tables for your VPC\. For more information, see [Gateway VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html) in the *Amazon Virtual Private Cloud User Guide*\. 

The following command creates an Amazon S3 endpoint\.

```
aws ec2 create-vpc-endpoint --vpc-id vpcid --service-name com.amazonaws.region.s3 \
  --route-table-ids routetableid
```

## Minimum Amazon S3 bucket permissions for AWS CodeArtifact<a name="s3-gateway-endpoint-permissions"></a>

The Amazon S3 gateway endpoint uses an IAM policy document to limit access to the service\. To allow only the minimum Amazon S3 bucket permissions for CodeArtifact, restrict access to the Amazon S3 bucket that CodeArtifact uses when you create the IAM policy document for the endpoint\.

The following table describes the Amazon S3 buckets you should reference in your policies to allow access to CodeArtifact in each region\.


****  

| Region | Amazon S3 Bucket ARN | 
| --- | --- | 
|  us\-east\-1  |  arn:aws:s3:::assets\-193858265520\-us\-east\-1  | 
|  us\-east\-2  |  arn:aws:s3:::assets\-250872398865\-us\-east\-2  | 
|  us\-west\-2  |  arn:aws:s3:::assets\-787052242323\-us\-west\-2  | 
|  eu\-west\-1  |  arn:aws:s3:::assets\-438097961670\-eu\-west\-1  | 
|  eu\-west\-2  |  arn:aws:s3:::assets\-247805302724\-eu\-west\-2  | 
|  eu\-west\-3  |  arn:aws:s3:::assets\-762466490029\-eu\-west\-3  | 
|  eu\-north\-1  |  arn:aws:s3:::assets\-611884512288\-eu\-north\-1  | 
|  eu\-south\-1  |  arn:aws:s3:::assets\-484130244270\-eu\-south\-1  | 
|  eu\-central\-1  |  arn:aws:s3:::assets\-769407342218\-eu\-central\-1  | 
|  ap\-northeast\-1  |  arn:aws:s3:::assets\-660291247815\-ap\-northeast\-1  | 
|  ap\-southeast\-1  |  arn:aws:s3:::assets\-421485864821\-ap\-southeast\-1  | 
|  ap\-southeast\-2  |  arn:aws:s3:::assets\-860415559748\-ap\-southeast\-2  | 
|  ap\-south\-1  |  arn:aws:s3:::assets\-681137435769\-ap\-south\-1  | 

You can use the `aws codeartifact describe-domain` command to fetch the Amazon S3 bucket used by a CodeArtifact domain\.

```
aws codeartifact describe-domain --domain mydomain
```

```
{
  "domain": {
    "name": "mydomain",
    "owner": "111122223333",
    "arn": "arn:aws:codeartifact:us-west-2:111122223333:domain/mydomain",
    "status": "Active",
    "createdTime": 1583075193.861,
    "encryptionKey": "arn:aws:kms:us-west-2:111122223333:key/a73que8sq-ba...",
    "repositoryCount": 13,
    "assetSizeBytes": 513830295,
    "s3BucketArn": "arn:aws:s3:::assets-787052242323-us-west-2"
  }
}
```

### Example<a name="s3-gateway-policy-example"></a>

The following example illustrates how to provide access to the Amazon S3 buckets required for CodeArtifact operations in the `us-east-1` region\. For other regions, update the `Resource` entry with the correct permission ARN for your region based on the table above\.

```
{
  "Statement": [
    {
      "Sid": "Access-to-specific-bucket-only",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::assets-193858265520-us-east-1/*"]
    }
  ]
}
```