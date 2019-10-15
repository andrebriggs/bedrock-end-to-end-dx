# SPK Integration Testing

This repo describes end to end tests that simulate user scenarios around `spk`. Consider this a work in progress.

## What needs to be setup in an _environment_? 

- Azure DevOps Project
- Terraform Environment
    - Azure Simple KeyVault (Bedrock)
    - ACR instance (accessible from AKS cluster)
 - Application code mono-repo
 - High level definition repo with Ingress Routes
 - Helm chart repo
	
## Orchestrating the integration tests with Azure Dev Ops

1. A yaml file defining an Azure pipeline. Tasks within this yaml file will orchestrate the setup/installation of any binaries. 
2. Next, yaml file tasks will use a configuration that can bootstrap `spk` with the knowledge of the testing environment.
3. Finally, a yaml task will call `spk` commands the same way an `spk` user would. 
4. Depending on the duration of the commands, a yaml task in the Azure pipeline will do an _assertion_ of expected results and exit the pipeline with a success or failure result. 
5. Azure DevOps has many hooks to allow notification of the pipeline result.

## Test Scenarios

### Create a Service
**High level steps that ignore details:**
1. Make sure `spk` is [initialized](https://github.com/CatalystCode/bedrock-end-to-end-dx#initializing-spk-tool) with knowledge of the test environment. 
2. Navigate to the appropriate to [mono-repo](https://github.com/CatalystCode/bedrock-end-to-end-dx#adopting-bedrock-in-existing-application-monorepo) root directory.
2. Call the `spk` [service create](https://github.com/CatalystCode/bedrock-end-to-end-dx#adding-a-service) command with any appropriate additional flags and arguments.
3. Automate the any git `add`, `commit`, and `push` commands (skip PR for automation)
3. Assert that a pipeline is created and it's build completed successfully on the Azure DevOps project

### _More to scenarios come..._

## Questions about Test environment

> _How do I expand to another AKS cluster from this single cluster AKS setup with Terraform?_

This scenario is something known as a "Day 2" operation. With Terraform state of the existing infrastructure is usually persisted. Working with your existing Terraform state file woul help achieve this.

> _How do I achieve the same scenarios when I have separate app code repos (not a mono-repo)?_

The `spk` tool supports creation and management of application code repositories that are not using a mono-repo pattern.

> _How exactly do we initialize the of global config file for SPK?_

The `spk` CLI is deisgned to support automation. Given that test environments are usually static once created the simpliest approach would be to have pre-existing config file with hardcoded values. Scaffolding this config file would help here. A more refined approach could be having commands on `spk` to allow imperative configuration. 

> _Will this test environment handle concurrent integration tests?_

We should be able to handle multiple tests at once. If necessary we can gate tests in Azure DevOps using variable group values.

> _How do we get SPK and additional tool on the build agent?_

The provided bash script will have the ability to download the latest public _release_ of SPK. In the future we will consider providing a Docker image that for a custom AzDO build agent that has all components bundled (SPK, Az CLI, Terraform, etc)