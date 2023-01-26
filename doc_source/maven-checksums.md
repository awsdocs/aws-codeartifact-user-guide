# Using Maven checksums<a name="maven-checksums"></a>

 When a Maven artifact is published to an AWS CodeArtifact repository, the checksum associated with each *asset* or file in the package is used to validate the upload\. Examples of assets are *jar*, *pom*, and *war* files\. For each asset, the Maven artifact contains multiple checksum files that use the asset name with an additional extension, such as `md5` or `sha1`\. For example, the checksum files for a file named `my-maven-package.jar` might be `my-maven-package.jar.md5` and `my-maven-package.jar.sha1`\. 

**Note**  
 Maven uses the term `artifact`\. In this guide, a Maven package is the same as a Maven artifact\. For more information, see [AWS CodeArtifact package](https://docs.aws.amazon.com/codeartifact/latest/ug/welcome.html#welcome-concepts-package)\. 

## Checksum storage<a name="maven-checksum-storage"></a>

CodeArtifact does not store Maven checksums as assets\. This means that checksums do not appear as individual assets in the output of the [ListPackageVersionAssets API](https://docs.aws.amazon.com/codeartifact/latest/APIReference/API_ListPackageVersionAssets.html) Instead, CodeArtifact\-computed checksums are available for each asset in all supported checksum types\. For example, part of the response of calling ListPackageVersionAssets on the Maven package version `commons-lang:commons-lang 2.1` is:

```
{
    "name": "commons-lang-2.1.jar",
    "size": 207723,
    "hashes": {
        "MD5": "51591549f1662a64543f08a1d4a0cf87",
        "SHA-1": "4763ecc9d78781c915c07eb03e90572c7ff04205",
        "SHA-256": "2ded7343dc8e57decd5e6302337139be020fdd885a2935925e8d575975e480b9",
        "SHA-512": "a312a5e33b17835f2e82e74ab52ab81f0dec01a7e72a2ba58bb76b6a197ffcd2bb410e341ef7b3720f3b595ce49fdd9994ea887ba08ff6fe21b2c714f8c405af"
    }
},
{
    "name": "commons-lang-2.1.pom",
    "size": 9928,
    "hashes": {
        "MD5": "8e41bacdd69de9373c20326d231c8a5d",
        "SHA-1": "a34d992202615804c534953aba402de55d8ee47c",
        "SHA-256": "f1a709cd489f23498a0b6b3dfbfc0d21d4f15904791446dec7f8a58a7da5bd6a",
        "SHA-512": "1631ce8fe4101b6cde857f5b1db9b29b937f98ba445a60e76cc2b8f2a732ff24d19b91821a052c1b56b73325104e9280382b2520edda4e7696698165c7e09161"
    }
},
        {
    "name": "maven-metadata.xml",
    "size": 121,
    "hashes": {
        "MD5": "11bb3d48d984f2f49cea1e150b6fa371",
        "SHA-1": "7ef872be17357751ce65cb907834b6c5769998db",
        "SHA-256": "d04d140362ea8989a824a518439246e7194e719557e8d701831b7f5a8228411c",
        "SHA-512": "001813a0333ce4b2a47cf44900470bc2265ae65123a8c6b5ac5f2859184608596baa4d8ee0696d0a497755dade0f6bf5e54667215a06ceae1effdfb7a8d30f88"
    }
}
```

 Even though checksums are not stored as assets, Maven clients can still publish and download checksums at the expected locations\. For example, if `commons-lang:commons-lang 2.1` was in a repository called `maven-repo`, the URL path for the SHA\-256 checksum of the JAR file would be:

```
/maven/maven-repo/commons-lang/commons-lang/2.1/commons-lang-2.1.jar.sha256
```

If you are uploading existing Maven packages \(for example, packages previously stored in Amazon S3\) to CodeArtifact using a generic HTTP client such as `curl`, it is not necessary to upload the checksums\. CodeArtifact will generate them automatically\. If you want to verify that the assets have been uploaded correctly, you can use the ListPackageVersionAssets API operation to compare the checksums in the response to the original checksum values for each asset\.

## Checksum mismatches during publishing<a name="maven-checksum-mismatch"></a>

Apart from assets and checksums, Maven artifacts also contain a `maven-metadata.xml` file\. The normal publishing sequence for a Maven package is for all assets and checksums to be uploaded first, followed by `maven-metadata.xml`\. For example, the publishing sequence for the Maven package version `commons-lang 2.1` described previously, assuming the client was configured to publish SHA\-256 checksum files, would be:

```
PUT commons-lang-2.1.jar
PUT commons-lang-2.1.jar.sha256
PUT commons-lang-2.1.pom
PUT commons-lang-2.1.pom.sha256
PUT maven-metadata.xml
PUT maven-metadata.xml.sha256
```

When uploading the checksum file for an asset, such as a JAR file, the checksum upload request will fail with a **400 \(Bad Request\)** response if there is a mismatch between the uploaded checksum value and the checksum value calculated by CodeArtifact\. If the corresponding asset doesn't exist, the request will fail with a **404 \(Not Found\)**\. To avoid this error, the asset must be uploaded first, then the checksum\.

When `maven-metadata.xml` is uploaded, CodeArtifact normally changes the status of the Maven package version from `Unfinished` to `Published`\. If a checksum mismatch is detected for any asset, CodeArtifact will return a **400 \(Bad Request\)** in response to the `maven-metadata.xml` publishing request\. This error may cause the client to stop uploading files for that package version\. If this occurs, and the `maven-metadata.xml` file is not uploaded, any assets of the package version already uploaded cannot be downloaded as the package version’s status will not be set to `Published` and will remain `Unfinished`\.

CodeArtifact allows adding additional assets to a Maven package version even after `maven-metadata.xml` has been uploaded and the package version status has been set to `Published`\. In this status, a request to upload a mismatched checksum file will also fail with a **400 \(Bad Request\)** response\. However, as the package version status has already been set to `Published`, you can download any asset from the package, including those for which the checksum file upload failed\. When downloading a checksum for an asset where the checksum file upload failed, the checksum value the client receives will be the checksum value calculated by CodeArtifact based on the uploaded asset data\.

CodeArtifact checksum comparison is case sensitive, and the checksums calculated by CodeArtifact are formatted in lowercase\. Therefore, if the checksum `909FA780F76DA393E992A3D2D495F468` is uploaded it will fail with a checksum mismatch because CodeArtifact does not treat it as equal to `909fa780f76da393e992a3d2d495f468`\.

## Recovering from checksum mismatches<a name="maven-checksum-mismatch-recovery"></a>

If a checksum upload fails due to a checksum mismatch, try one of the following to recover:
+ Run the command that publishes the Maven artifact again\. This might work if a network issue corrupted the checksum file\. If this resolves the network issue, the checksum matches and the download is successful\.
+ Delete the package version and then republish it\. For more information, see [DeletePackageVersions](https://docs.aws.amazon.com/dms/latest/APIReference/API_DeletePackageVersions.html) in the *AWS CodeArtifact API Reference*\.