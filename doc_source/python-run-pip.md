# Run pip<a name="python-run-pip"></a>

To run `pip` commands, you must configure `pip` with CodeArtifact\. For more information, see [Configure clients with the login command](python-configure.md)\.

Assuming that a package is present in your repository or one of its upstream repositories, you can install it with `pip install`\. For example, use the following command to install the `requests` package\.

```
pip install requests
```

Use the `-i` option to temporarily revert to installing packages from [https://pypi\.org](https://pypi.org) instead of your CodeArtifact repository\.

```
pip install -i https://pypi.org/simple requests
```