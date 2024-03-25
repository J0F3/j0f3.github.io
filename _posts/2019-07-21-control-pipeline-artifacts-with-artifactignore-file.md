---
title: Control pipeline artifacts upload with the .artifactignore file in Azure DevOps Pipelines
classes: wide
excerpt: "To control with files and directories are upload as pipeline artifacts in an Azure DevOps Pipeline the .artifactignore file can be used."
header:
  teaser: /assets/images/adopipelines2.jpg
date: 2019-07-21
tags:
  - Azure DevOps
  - Pipelines
  - CI/CD
  - Artifacts
---

## Build vs. Pipeline Artifacts

With the classic build and release pipelines, which are still know form the time back where Azure DevOps was named VSTS (Visual Studio Team Services) and which were created then with the graphical editor, the build pipeline normally published at the end an artifacts with the ['Publish Build Artifacts'](https://docs.microsoft.com/en-us/azure/devops/pipelines/artifacts/build-artifacts?view=azure-devops&tabs=yaml){:target="_blank"} task. This was back then actually also the only way to publish build artifacts. When using this task it was quite common that only what should be published as build artifact was copied forehand to the artifacts staging directory (referenced by the Build.ArtifactStagingDirectory variable) to which then the 'Publish Build Artifacts' task was pointed to.

With the introduction of the new YAML based multistage pipelines also a new variation of artifacts was introduced - the ['Pipeline Artifacts'](https://docs.microsoft.com/en-us/azure/devops/pipelines/artifacts/pipeline-artifacts?view=azure-devops&tabs=yaml){:target="_blank"}. They have also their own task to upload and download artifacts which can very easy specified in the YAML by the 'upload' and 'download' keywords. Because the new pipeline artifacts are much faster and more lightweight it make sense to use them now instead of the classic build artifacts. Microsoft even recommends to upgrade to the new pipeline artifacts now.

## What works different with the new Pipeline Artifacts?

While you can still first copy the files which you want to publish to the the artifacts staging directory, that is not really recommended anymore with the new publish pipeline artifacts task. Instead you should point the task directly to the location where the artifacts are get created and use integrated functionality to limit which files are get uploaded. This is done with the special '.artifactignore' file.

## So what is the .artifactignore file?

The .artifactignore is actually very similar to a .gitignore file but instead of defining which files should be ignored by the Git version control system the .artifactignore tells the publish pipeline artifacts task in an Azure Pipeline which files should be ignored when uploading the artifact. At the end it is a simple text file which include some statement about the files or directories which should be excluded form the artifact.

Here is an example to exclude everything than the content of the 'build' directory:

```text
**/*
!/build/**
```

The first line excludes everything and the second explicit includes again everything inside the 'build' directory.

The syntax of the file is the same a for a .gitignore file which is quit good [documented](https://git-scm.com/docs/gitignore) in the Git documentation.

## Where should I place that file?

While the principle of the .artifactignore file is quite simple I struggled, at the beginning, a little bit about the correct placing of the file. While it may be quite obvious that you want to include the file in Git repository to have it versioned it may not be that clear that the file must not be always in the root of your repository (where the .gitingore file normally is). Where you save the .artifactignore file really depends which path you have specified for the publish pipeline artifact task in your pipeline definition.
For the example of the Microsoft documentation where the task looks like:

```yaml
steps:
- publish: $(System.DefaultWorkingDirectory)/bin/WebApp
  artifact: WebApp
```

In this case you must place the .artifactignore in the /bin/WebApp directory in your repository (or copy it somehow to there before running the publish task) otherwise the publish task will not see the .artifactignore file and it would not happened what you expect.

## Example

Lets see how it works with an other, real world, example:  
![GitRepoExample](/assets/images/gitrepo.png)

A simple Git repository with some ARM templates in the ARM directory and some scripts used by the pipeline in the pipeline-scripts directory. The only thing that I want to include in the pipeline artifact is the ARM directory. So I create a .artifactignore in the root of the repository with the following content:

```text
**/*
!ARM/**
```

In the pipeline the upload task points also to the root of the source directory:

```yaml
steps:
  - publish: $(Build.SourcesDirectory)
    artifact: ARMTemplates
```

And finally when the pipeline runs we can see that the .artifactignore is detected and processed by the upload task:
![pipeline](/assets/images/azpipelineartifactsex.png)

So the .artifactignore is actually quite simple but very useful feature of the new pipeline artifacts. Furthermore the file can also be used together with the [Uinversal Packages](https://devblogs.microsoft.com/devops/getting-started-with-universal-packages/).
