# PublishAzureArtifact.ado
Use this task to download, or package and publish Universal Packages. Universal Packages allow you to package any number of files of any type and share them with your team. 

## Parameters:

The pipeline requires the following parameters to be defined:

| Name  | type | Display Name | Default | Values | Optional/Required | Comments |
| :------------- | :-------------: | :-------------: |:-------------: | :-------------: | :-------------: | :------------- |
| publishDirectory   | string | publishDirectory |  |  | Required |  Artifact source |
| feedsToUsePublish  | string | feedsToUsePublish | | | Required | internal or external feed for artifact |
| vstsFeedPublish    | string | vstsFeedPublish | | | Required | feed endpoint or source |
| vstsFeedPackagePublish  | string | vstsFeedPackagePublish | |  | Required | Package to be published  |
| versionOption  | string | versionOption | patch | major, minor, patch, custom | Required | Option to use version |
| versionPublish  | string | Custom versions | |  | Required | version to be published  |
| packagePublishDescription  | string | packagePublishDescription | |  | Required | Description for publishing  |
| verbosity | string | verbosity | |  | Required | verbose type |
| publishedPackageVar | string | publishedPackageVar | | | Required | contain the published package name and version |



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
