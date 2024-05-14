# PublishAzureArtifact.ado
Use this task to download, or package and publish Universal Packages. Universal Packages allow you to package any number of files of any type and share them with your team. 

## Parameters:

The pipeline requires the following parameters to be defined:

| Name  | Type | Display Name | Default | Values | Optional/Required | Comments |
| :------------- | :------------- | :------------- |:------------- | :------------- | :------------- | :------------- |
| publishDirectory   | string | Path to file(s) to publish | $(Build.ArtifactStagingDirectory) |  | Required |  Artifact source |
| feedsToUsePublish  | string | Feed location | internal | internal or external | Required | internal or external feed for artifact. Input alias: internalOrExternalPublish. Specifies a feed from this collection or another collection in Azure Artifacts. |
| vstsFeedPublish    | string | Destination Feed | | | Required (when internalOrExternalPublish = internal) | Specifies the project and the feed's name/GUID to publish to. |
| vstsFeedPackagePublish  | string | vstsFeedPackagePublish | |  | Required (when internalOrExternalPublish = internal) | Specifies a package ID to publish or creates a new package ID if you've never published a version of this package before. Package names must be lower case and can only use letters, numbers, and dashes (-) |
| versionOption  | string | versionPublishSelector | patch | major, minor, patch, custom | Required | Specifies a version increment strategy. The custom value to input your package version manually. For new packages, the first version will be 1.0.0 if you specify major, 0.1.0 if you specify minor, or 0.0.1 if you specify patch. |
| versionPublish  | string | Custom versions | |  | Required when versionPublishSelector = custom | Specifies a custom version schema for the package.   |
| packagePublishDescription  | string | Description | |  | Optional | Specifies the description of the package contents and/or the changes made in this version of the package.  |
| verbosity | string | Verbosity | None | None, Trace, Debug, Information, Warning, Error, Critical | Optional | verbose type |
| publishedPackageVar | string | Package Output Variable | | | Optional | Specifies a name for the variable that will contain the published package name and version. |



These parameters provide multiple use case options for the template, enable/disable flags for the utilization of different templates as per the requirements.


## Use Cases

You can directly call a particular template as per the requirement. for example: 

  ```yaml
  # azure-pipeline.yml
  resources:
  repositories:
    - repository: Template
      type: github
      name: your_username/PublishAzureArtifact.ado
      ref: <respective branch name>
      endpoint: 'githubServiceConnectioNname'

  steps:
  - checkout: self
    displayName: Clean Checkout
    clean: true
  # Package metadata will be an xml file with all the package informations & dependencies
  - script: |
       Version=$(cat ./metadata.yml | awk '/version:/ {print $2}')
       echo "##vso[task.setvariable variable=Version]$Version"
       echo $Version
    displayName: 'Fetch Version From Metadata'  
  # passing the parameters
  - template: PublishAzureArtifact.yml
    parameters:
      publishDirectory: '${{ parameters.publishDirectory }}'
      feedsToUsePublish: '${{ parameters.feedsToUsePublish }}'
      vstsFeedPublish: '${{ parameters.vstsFeedPublish }}'
      vstsFeedPackagePublish: '${{ parameters.vstsFeedPackagePublish }}'
      versionOption: '${{ parameters.versionOption }}'
      versionPublish: '${{ parameters.versionPublish }}'
      packagePublishDescription: '${{ parameters.packagePublishDescription }}'
      verbosity: '${{ parameters.verbosity }}'
      publishedPackageVar: '${{ parameters.publishedPackageVar }}'
        
Make sure to adjust the repository name, branch name, and parameter values according to your project's requirements.

  ```
