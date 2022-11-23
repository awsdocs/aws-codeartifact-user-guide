# Use CodeArtifact from a VPC<a name="use-codeartifact-from-vpc"></a>

To call CodeArtifact APIs using the AWS CLI or SDK using a VPC Endpoint, you must override the default endpoint used by CodeArtifact\. Follow the instructions in [Configure the AWS CLI to use the `codeartifact.api` endpoint](#use-codeartifact-from-vpc-api-endpoint) to get the VPC Endpoint hostname and configure the CLI with it\.

If you cannot or do not want to enable private DNS on your `com.amazonaws.region.codeartifact.repositories` VPC endpoint that you created in [Create VPC endpoints for CodeArtifact](create-vpc-endpoints.md), you must use a different configuration for the repositories endpoint to use CodeArtifact from a VPC\. Follow the instructions in [Use the `codeartifact.repositories` endpoint without private DNS](#use-codeartifact-from-vpc-no-private-dns) to configure CodeArtifact if the `com.amazonaws.region.codeartifact.repositories` endpoint does not have private DNS enabled\.

## Configure the AWS CLI to use the `codeartifact.api` endpoint<a name="use-codeartifact-from-vpc-api-endpoint"></a>

Use the following instructions to override the default CodeArtifact hostname with the hostname used by the `com.amazonaws.region.codeartifact.api` VPC endpoint\.

1.  Run the following command to find a VPC endpoint to use to override the hostname\.

   ```
   $ aws ec2 describe-vpc-endpoints --filters Name=service-name,Values=com.amazonaws.region.codeartifact.api \
     --query 'VpcEndpoints[*].DnsEntries[*].DnsName'
   ```

    The output looks like the following\.

   ```
   [
     [
       "vpce-0743fe535b883ffff-76ddffff.api.codeartifact.us-west-2.vpce.amazonaws.com",
       "vpce-0743fe535b883ffff-76edffff-us-west-2a.api.codeartifact.us-west-2.vpce.amazonaws.com"
     ]
   ]
   ```

   In this example, you can use either hostname to override the `com.amazonaws.region.codeartifact.api` endpoint\.

1. If you use the CodeArtifact AWS CLI, use the `codeartifact login` command to override the default CodeArtifact hostname with the Amazon VPC endpoint by passing endpoint into the `--endpoint-url` parameter\. See the following example\.
**Warning**  
The `login` command does not support Maven or Gradle\. To configure those package managers, see [Using CodeArtifact with Maven](using-maven.md)\.

   ```
   aws codeartifact login --tool npm --domain mydomain --domain-owner 111122223333 --repository myrepo --endpoint-url VPC_endpoint
   ```

   Replace *VPC\_endpoint* with your Amazon VPC endpoint, prefixed with `https://`\. See the following example endpoint\.

   ```
   https://vpce-0743fe535b883ffff-76ddffff.api.codeartifact.us-west-2.vpce.amazonaws.com
   ```

   If you use the SDK, consult your SDK documentation to learn how to override a hostname\. How to do this varies by the language that you use\.

## Use the `codeartifact.repositories` endpoint without private DNS<a name="use-codeartifact-from-vpc-no-private-dns"></a>

If you cannot or do not want to enable private DNS on your `com.amazonaws.region.codeartifact.repositories` VPC endpoint that you created in [Create VPC endpoints for CodeArtifact](create-vpc-endpoints.md), you must follow these instructions to configure your package manager with the correct CodeArtifact URL\.

1.  Run the following command to find a VPC endpoint to use to override the hostname\.

   ```
   $ aws ec2 describe-vpc-endpoints --filters Name=service-name,Values=com.amazonaws.region.codeartifact.repositories \
     --query 'VpcEndpoints[*].DnsEntries[*].DnsName'
   ```

    The output looks like the following\.

   ```
   [
     [
       "vpce-0743fe535b883ffff-76ddffff.d.codeartifact.us-west-2.vpce.amazonaws.com"
     ]
   ]
   ```

1. Update the VPC endpoint path to include the package format, your CodeArtifact domain name, and CodeArtifact repository name\. See the following example\.

   ```
   https://vpce-0743fe535b883ffff-76ddffff.d.codeartifact.us-west-2.vpce.amazonaws.com/format/d/domain_name-domain_owner/repo_name
   ```

   Replace the following fields from the example endpoint\.
   + *format*: Replace with a valid CodeArtifact package format, for example, `npm` or `pypi`\.
   + *domain\_name*: Replace with the CodeArtifact domain that contains the CodeArtifact repository that hosts your packages\.
   + *domain\_owner*: Replace with the ID of the owner of the CodeArtifact domain, for example, `111122223333`\.
   + *repo\_name*: Replace with the CodeArtifact repository that hosts your packages\.

   The following URL is an example npm repository endpoint\.

   ```
   https://vpce-0dc4daf7fca331ed6-et36qa1d.d.codeartifact.us-west-2.vpce.amazonaws.com/npm/d/domainName-111122223333/repoName
   ```

1. Configure your package manager to use the updated VPC endpoint from the previous step\. You must configure the package manager without using the CodeArtifact `login` command\. For configuration instructions for each package format, see the following documentation\.
   + npm: [Configuring npm without using the login command](npm-auth.md#configuring-npm-without-using-the-login-command)
   + nuget: [Configure nuget or dotnet without the login command](nuget-cli.md#nuget-configure-without-login)
   + pip: [Configure pip without the login command](python-configure-pip.md#python-configure-without-pip)
   + twine: [Configure and use twine with CodeArtifact](python-configure-twine.md)
   + Gradle: [Use CodeArtifact with Gradle](maven-gradle.md)
   + mvn: [Use CodeArtifact with mvn](maven-mvn.md)