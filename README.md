# Optimizely (formerly Episerver) DXP YAML Pipelines
Reusable multi-stage YAML pipelines to setup CI/CD for Optimizely DXP deployments

The pipelines are ideal for projects using a trunk based branching strategy, however the triggers can be tweaked to work with other branching models.

## Continuous Integration

**Update** The `Integration` pipeline now supports direct deploy to the Integration environment using the Deployment API code package approach. This is the recommended approach to deploying to Integration.

The `Integration-WebDeploy` pipeline creates a web package and deploys to the Integration environment using the Azure App Service Deploy task. Create a service connection to your DXP Azure instance on Azure DevOps. The service principal details required for the connection can be requested from Episerver Managed Services. This pipeline is provided for legacy purposes.

The Integration pipelines are triggered when code is merged to `master`.

## Release

The `Release` pipeline is used to deploy planned releases. It 
- is triggered on any change to `release/*` branch. Use consistent naming convention for release branch name e.g. `release/1.0` for deployment history purposes.
- creates a Nuget package and uploads it to the Episerver DXP for deployment. The package name will be in the format _[app name].cms.app.<releasebranchname.buildnumber.revision>.nupkg_, e.g. customer.cms.app.1.0.20200527.1.nupkg
- allows for staged deployments to Preproduction and Production environments (upon approval) 
- supports Smooth deployments / Zero downtime deployments

### Variables

Create variable groups 'dxp-inte' and 'dxp-release' for the 2 pipelines respectively, with the following variables (Deployment API credentials are generated from the DXP Portal):
- ProjectId
- ApiKey
- ApiSecret (secret variable)

## Web.config transforms

The `msbuild` task in the pipelines will apply the Release transformation on the web.config file.

When deploying to the respective environments, the DXP Deployment API will apply the environment transform files on the web.config in the code package. Set the BuildAction for environment transform files to `Content` so that they are included in the package.

## Environments

Deployments history can be viewed from the _Pipelines -> Environments_ page on Azure DevOps. The deployed version <releasebranchname.buildnumber.revision> will be displayed against the respective environments.

Approvals can be setup on the Preproduction and Production environments to ensure approvers grant approval before deployment to the respective environment. 

