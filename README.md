# Catalog Services

This repository contains the catalog service Helm Chart templates for the service offerings provided through the Self-Service-Portal. These services are provided as-is and need only be pushed to a helm package registry so that they can be deployed by the cloud-agnostic platform backend.

Each service consists of Kubernetes resources bundled as helm templates which are infused with values which can be default or dynamic through the `values.yaml` file or through helm cli `--set` arguments. 

Some services are forked from existing ones while others are custom built. All charts require to be modified to enable the cloud-agnostic platform to deploy and manage them. In cases where a chart exposes a public-facing web interface, a modification is required to secure it e.g ingress authentication.

## Prerequisites
- Have Helm installed on your machine.
  - For Helm documentation go [here](https://helm.sh/docs/)
- Have `helm cm-push plugin` installed on your machine [(found here)](https://github.com/chartmuseum/helm-push).
- A helm registry into which you can publish packages. To add your registry use the `helm repo add` command.

## Deployment

1. Clone the repository locally
2. For each service directory:
   1. Navigate to a chart/service and check the Chart.yaml for dependencies to understand whether dependencies need to be built `helm dependency package`  
   2. Execute `helm package [service_directory]/`
   3. Execute `helm cm-push [generated_tgz_file] [helm_registry_name]`.

| Parameter | Description |
|:-|:-|
| [service_directory] | The folder of each of the catalog-service |
| [generated_tgz_file] | The .tgz file that `helm package` command will generate |
| [helm_registry_name] | The alias of your added helm registry that you can push to |

## Extra catalog services

Some services/charts have been removed due to licensing conflicts with other platform components. However, these can be added separately by the user/admin who is deploying the platform.

There are four main steps to follow in order to introduce a new catalog service.

1. **Optional** - The new catalog service chart requires to have been already modified accordingly in order to be compatible with the platform. These modifications are related to Kubernetes resources such as extra labelsa and/or templates which allow the platform to manage them or enable extra features such as authentication.
2. Find git repositories with Helm Charts that are compatible per step 1.
3. Package and publish those charts in a helm registry, either private or public.
4. Connect to the platform [backend/metadata database](https://code.europa.eu/bdti/bdti/-/blob/master/values.yaml#L288) and modify the catalog service tables to include the new services in the catalog. (See example records in the [job-init-db docker image](https://code.europa.eu/bdti/docker/job-init-db/-/blob/master/index.js))

## Common Issues 
 
In rare cases, chart providers such as bitnami, might keep certain chart versions in separate helm repositories. This might happen due to storage or organizational issues. That means, before executing a dependency build, specific repositories might have to be added `helm add repo <url>`, `helm repo update`. More [information](https://github.com/bitnami/charts/issues/10539) regarding bitnami's case.

## Contributing

| :warning: WARNING          |
|:---------------------------|
| This repository is being mirrored to a public repository. All protected branches are being automatically mirrored. Do not create protected branches other than the main branch and release branches. |