# Support for npm\-compatible package managers<a name="npm-other-clients"></a>

These other package managers are compatible with CodeArtifact and work with the npm package format and npm wire protocol: 
+  [pnpm package manager](https://pnpm.js.org)\. The latest version confirmed to work with CodeArtifact is 3\.3\.4, which was released on May 18, 2019\. 
+  [Yarn package manager](https://yarnpkg.com/)\. The latest version confirmed to work with CodeArtifact is 1\.21\.1, which was released on December 11, 2019\. 

**Note**  
We recommend using Yarn 2\.x with CodeArtifact\. Yarn 1\.x does not have HTTP retries, which means it is more susceptible to intermittent service faults which result in 500\-level status codes or errors\. There is no way to configure a different retry strategy for Yarn 1\.x, but this has been added in Yarn 2\.x\. You can use Yarn 1\.x, but you may need to add higher\-level retries in build scripts\. For example, running your yarn command in a loop so that it will retry if downloading packages fails\.