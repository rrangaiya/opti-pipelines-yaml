# Episerver DXP YAML Pipelines
Reusable multi-stage YAML pipelines to setup CI/CD for Episerver DXP deployments

The pipelines are ideal for projects using a trunk based branching strategy, however the triggers can be tweaked to work with other branching models.

## Continuous Integration

The Integration pipeline is setup to run when code is merged to `master`. It creates a web package and deploys to the Integration environment using the Azure App Service Deploy task. The main reason for this approach is to allow for a faster deployment to this environment.

Create a service connection to your DXP Azure instance on Azure DevOps. The service principal details required for the connection can be requested from Episerver Managed Services.

## Release

The `Release` pipeline is used to deploy planned releases. It 
- is triggered on any change to `release/*` branch. Use consistent naming convention for release branch name e.g. `release/1.0` for deployment history purposes.
- creates a Nuget package and uploads it to the Episerver DXP for deployment. The package name will be in the format _[app name].cms.app.<releasebranchname.buildnumber.revision>.nupkg_, e.g. customer.cms.app.1.0.20200527.1.nupkg
- allows for staged deployments to Preproduction and Production environments (upon approval) 

### Web.config transforms

The DXP Deployment API will apply environment transform files on the web.config in the Nuget package. Set the BuildAction for environment transform files to `Content` so that they are included in the package.

### Variables

Add the following variables to the Release pipeline for the Deployment API credentials (from the DXP Portal):
- ProjectId
- ApiKey
- ApiSecret (secret variable)

## Environments

Deployments history can be viewed from the _Pipelines -> Environments_ page on Azure DevOps. The deployed version <releasebranchname.buildnumber.revision> will be displayed against the respective environments

