# GitOps: Update Image Tag

This GitHub Action updates the Image Tag in a GitOps repository. It's designed to streamline the process of modifying image tags in your infrastructure-as-code (IaC) setups.

## Use Case

-  ✅ You have a GitOps repository that contains the IaC for your infrastructure.
-  ✅ You have a CI/CD pipeline that builds and pushes Docker images to a registry. 
-  **This is what this action does →** You want to update the image tag in the GitOps repository whenever a new image is pushed to the registry. 

## Mandatory Inputs

* `REPOSITORY_NAME`: Owner and repository name of the GitOps repository (e.g., `AdWert-IT/infrastructure`).
* `VALUES_FILE_PATH`: Path to the values file in the GitOps repository (e.g., `/deployments/helmCharts/projects/AdWert-IT/values.yaml`).
* `VALUE_NAME`: Name of the value in the values file (e.g., `frontendImageTag`).
* `TAG`: Name of the Image Tag (e.g., `v1.0.0` or `e0f9a8b`).

## Optional Inputs

* `ACCESS_TOKEN`: (Personal) Access Token for the GitOps repository. Defaults to `${{ github.token }}`. A personal access token is required if the GitOps repository is private or if the action is triggered by a workflow in another repository.
* `BRANCH`: Branch of the GitOps repository. Defaults to `main`.
* `USERNAME`: Username for the GitOps repository. Defaults to `${{ github.actor }}`.

## Usage

To use this action, create a workflow file (e.g., `.github/workflows/update-image-tag.yml` ) in your repository with the following content:

```yaml
name: Update Image Tag in GitOps Repository

on:
  workflow_run:
    workflows: ["Build image"]
    branches: [main]
    types:
        - completed

jobs:
    update-image-tag:
        if: ${{ github.event.workflow_run.conclusion == 'success' }}
        runs-on: ubuntu-latest

        steps:
            - name: GitOps - Update Image Tag
                uses: adwert-it/gitops-image-update@v1
                with:
                    REPOSITORY_NAME: 'AdWert-IT/infrastructure'
                    ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
                    BRANCH: "master"
                    VALUES_FILE_PATH: 'deployments/helmCharts/projects/richtungswechsler/values.yaml'
                    VALUE_NAME: 'websiteImageSHA'
                    TAG: '${{ github.sha }}' 
```

In this particular case the action is triggered by the completion of the workflow "Build image" on the main branch. The action then updates the image tag in the values file of the GitOps repository. The image tag is set to the SHA of the commit that triggered the workflow.
