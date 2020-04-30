# GitHub Action for Azure Resource Manager (ARM) deployment

A GitHub Action to deploy ARM templates.

![build and publish](https://github.com/whiteducksoftware/azure-arm-action/workflows/build-release/badge.svg)


## Dependencies

* [Checkout](https://github.com/actions/checkout) To checks-out your repository so the workflow can access any specified ARM template.

## Inputs

* `creds` **Required** Paste output of `az ad sp create-for-rbac -o json` as value of secret variable: AZURE_CREDENTIALS

* `resourceGroupName` **Required** Provide the name of a resource group.

* `templateLocation` **Required** Specify the path to the Azure Resource Manager template.

* `deploymentMode` Incremental (only add resources to resource group) or Complete (remove extra resources from resource group). Default: `Incremental`.
  
* `deploymentName` Specifies the name of the resource group deployment to create.

* `parameters` Specify the path to the Azure Resource Manager parameters file.

## Usage

```yml
- uses: whiteducksoftware/azure-arm-action@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
    resourceGroupName: <YourResourceGroup>
    templateLocation: <path/to/azuredeploy.json>
```

## Example

```yml
on: [push]
name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: whiteducksoftware/azure-arm-action@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        resourceGroupName: github-action-arm-rg
        templateLocation: ./azuredeploy.json
        parameters: <path/to/parameters.json>
```
