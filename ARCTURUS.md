# Building and Deploying HCap ADO Agents

The following instructions are for building the images and deploying a scale set to be used in Arcturus for running the HCap ADO agents. You can build these agents in Linux or Windows with the following:

1. Packer
2. Powershell
3. `az` CLI

## Building

[These instructions](docs/create-image-and-azure-resources.md) will guide you through the process of creating the images and deploying the scale set.

For building the images please use the following variables:
- `SubscriptionId` - `4ca421ab-c51e-4251-a8fe-52be96cc20d8`
- `ResourceGroupName` - `arcadoagentbuild`for building the images, and `arcadoagents` for deploying the scale set
- `AzureLocation` - `southcentralus`
- `ImageType` - `Windows2022`

Notes on building:
- You do not need to run the post-generation scripts.
- When the image is built, navigate to the Azure portal and move the image from the `arcadoagentbuild` resource group to the `arcadoagents` resource group.

## Deploying

[These instructions](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/scale-set-agents?view=azure-devops) cover the deployment of a scale set to be used in Arcturus ADO.

The configuration for the scale set is as follows:
```
az vmss create \
--name arcagentspool \
--resource-group arcadoagents \
--image Runner-Image-Windows2022 \
--vm-sku Standard_DS4_v2 \
--storage-sku StandardSSD_LRS \
--instance-count 2 \
--disable-overprovision \
--upgrade-policy-mode manual \
--single-placement-group false \
--platform-fault-domain-count 1 \
--load-balancer "" \
--orchestration-mode Uniform
```

ADO has already been configured to use the scale set for running the agents. The agents will be automatically registered with the ADO organization when they are started. DO NOT MODIFY THE SCALE SET MANUALLY OR IT WILL BREAK THE ORCHESTRATION PROVIDED BY ADO.