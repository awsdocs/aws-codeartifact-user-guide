# Delete a package version<a name="delete-package"></a>

You can delete one or more package versions at a time using the `delete-package-versions` command\. The following deletes versions `4.0.0`, `4.0.1`, and `5.0.0` of the npm package named `my-package` in the `my-repo` in the `my-domain` domain:

```
aws codeartifact delete-package-versions --domain my-domain --domain-owner domain-owner-id --repository my-repo \
  --format npm --package my-package --versions 4.0.0 4.0.1 5.0.0
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
aws codeartifact list-package-versions --domain my-domain --domain-owner domain-owner-id --repository my-repo \
   --format npm --package my-package
```