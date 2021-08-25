# Run twine<a name="python-run-twine"></a>

Before using `twine` to publish Python package assets, you must first configure CodeArtifact permissions and resources\.

1. Follow the steps in the [Setting up with AWS CodeArtifact](get-set-up-for-codeartifact.md) section to configure your AWS account, tools, and permissions\.

1. Configure `twine` by following the steps in [Configure clients with the login command](python-configure.md)\.

After you configure `twine`, you can run `twine` commands\. Use the following command to publish Python package assets\.

```
twine upload --repository codeartifact mypackage-1.0.tgz
```

For information about how to build and package your Python application, see [Generating Distribution Archives](https://packaging.python.org/tutorials/packaging-projects/#generating-distribution-archives) on the Python Packaging Authority website\.