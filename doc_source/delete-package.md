# Delete a package version<a name="delete-package"></a>

You can delete one or more package versions at a time using the `delete-package-versions` command\. The following deletes versions `4.0.0`, `4.0.1`, and `5.0.0` of the pypi package named `my-package` in the `my_repo` in the `my_domain` domain:

```
aws codeartifact delete-package-versions --domain my_domain --domain-owner 111122223333 \
--repository my_repo --format pypi \
--package my-package --versions 4.0.0 4.0.1 5.0.0
```

Sample output:

```
{
   "successfulVersions": {
      "4.0.0": {
         "revision": "oxwwYC9dDeuBoCt6+PDSwL6OMZ7rXeiXy44BM32Iawo=",
            "status": "Deleted"
      },
      "4.0.1": {
         "revision": "byaaQR748wrsdBaT+PDSwL6OMZ7rXeiBKM0551aqWmo=",
            "status": "Deleted"
      },
      "5.0.0": {
         "revision": "yubm34QWeST345ts+ASeioPI354rXeiSWr734PotwRw=",
            "status": "Deleted"
      }
   },
   "failedVersions": {}
}
```

You can confirm that the versions were deleted by running `list-package-versions` for the same package name:

```
aws codeartifact list-package-versions --domain my_domain --domain-owner 111122223333 \
--repository my_repo --format pypi --package my-package
```

## Delete an npm package version<a name="delete-npm-package"></a>

 To delete an npm package version, set the `--format` option to `npm`\.

To delete a package version in a scoped npm package, use the `--namespace` option to specify the scope\. For example, to delete the package `@types/react`, use `--namespace types`\. The `@` symbol must be omitted when using `--namespace`\.

```
aws codeartifact delete-package-versions --domain my_domain --domain-owner 111122223333 \
--repository my_repo --format npm --namespace types \
--package react --versions 0.12.2
```

## Delete a Maven package version<a name="delete-maven-package"></a>

To delete Maven package versions, set the `--format` option to `maven` and specify the package to delete by passing the Maven group ID with the `--namespace` option and the Maven artifactID with the `--name` option\. For example, to delete a single version of `com.google.guava:guava`:

```
 aws codeartifact delete-package-versions --domain my_domain --domain-owner 111122223333 \
--repository my_repo --format maven --namespace com.google.guava \
--package guava --versions 27.1-jre
```