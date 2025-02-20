# Terraform

## Table of contents

- [Overview of Terraform](#overview-of-terraform)
- [Deploy infrastructure with Terraform](#deploy-infrastructure-with-terraform)
- [Uninstalling and re-installing Terraform locally with Chocolately](#uninstalling-and-re-installing-terraform-locally-with-chocolately)
- [Upgrading Terraform in a repository](#upgrading-terraform-in-a-repository)
- [Error messages](#error-messages)

## Overview of Terraform

Terraform manages infrastructure with configuration files (.tf files). This method of management is called **IaC (Infrastructure as Code)**. This is instead of managing your infrastructure through a graphical user interface, such as Azure Portal.

An advantage of IaC is you can version, reuse and share your configurations. Terraform is HashiCorp's IaC tool. Terraform can manage infrastructure on *multiple cloud platforms* (known as **providers**). You can find providers in the *Terraform Registry*.

Terraform's **state** allows you to track resource changes throughout your deployments. You can commit your configurations to version control to *safely collaborate on infrastructure*.

Providers define *individual units of infrastructure* (e.g compute instances or private networks) as **resources**. You can compose resources from different providers into reusable Terraform configurations called **modules**. Terraform providers *automatically calculate dependencies between resources* to create or destroy them in the correct order.

Terraform's configuration language is **declarative**, meaning that it *describes the desired end-state for your infrastructure*. This is quite different to *procedural programming languages*, which require step-by-step instructions to perform tasks.

## Deploy infrastructure with Terraform

- Identify the infrastructure for the project
- Write the configuration for the infrastructure
- ```terraform init``` to install the plugins Terraform needs to manage the infrastructure
- ```terraform plan``` to review the changes Terraform will make to match the configuration
- ```terraform apply``` to make the planned changes

### The state file

Terraform keeps track of the **real infrastructure** in a state file, which acts as a **source of truth** for the environment. Terraform uses the state file to determine the changes to make to your infrastructure so that it will match your configuration.

## Uninstalling and re-installing Terraform locally with Chocolately

Make sure you are in **admin mode**. Uninstall the current version of Terraform:

```bash
choco uninstall terraform
```

Install the version required:

```bash
choco install terraform --version=x.x.x
```

If this version is going to be a downgrade, you will need the flag below:

```bash
choco install terraform --version=x.x.x --allow-downgrade
```

Verify the installation worked:

```bash
terraform version
```

You can find the [latest version of Terraform here](https://developer.hashicorp.com/terraform/install).

## Upgrading Terraform in a repository

- Ensure that the **repository version is bumped up**
- **Deploy to every single environment** because this is normally a breaking change
- Terraform is sensitive when it comes to versioning!

## Error messages

### Unsupported Terraform Core version

```plaintext
Initializing the backend... ╷ │ Error: Unsupported Terraform Core version │ │ on version.tf line 2, in terraform: │ 2: required_version = "~> 1.5.2" │ │ This configuration does not support Terraform version 1.8.4. To proceed, either choose another supported Terraform │ version or update this version constraint. Version constraints are normally set for good reason, so updating the │ constraint may lead to other errors or unexpected behavior.
```

My local version of Terraform didn't match the specified version in my version.tf file:

```terraform
terraform {
  required_version = "~> 1.5.2"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.5.0"
    }
  }
}
```

### Error acquiring the state lock

```plaintext
│ Error: Error acquiring the state lock
│ 
│ Error message: state blob is already locked
│ Lock Info:
│   ID:        e4ca726e-3bdf-4433-1467-d9caf9640a5a
│   Path:      millie-container/terraform.tfstate
│   Operation: OperationTypePlan
│   Who:       vsts@fv-az719-784
│   Version:   1.10.5
│   Created:   2025-01-30 15:02:41.801615579 +0000 UTC
│   Info:      
│ 
│ 
│ Terraform acquires a state lock to protect the state from being written
│ by multiple users at the same time. Please resolve the issue above and try
│ again. For most commands, you can disable locking with the "-lock=false"
│ flag, but this is not recommended.
╵

##[warning]Can't find loc string for key: TerraformPlanFailed
##[error]Error: TerraformPlanFailed 1
Finishing: Create Terraform Plan
```

Solution found [here](https://stackoverflow.com/questions/65595852/terraform-statefile-is-locked-how-do-i-unlock-it). You can **manually unlock the state** using the force-unlock command:

```bash
terraform force-unlock LOCK_ID
```

The Lock ID is usually shown in the error message. It may not work if your state is local and locked by a local process. If that is the case, kill that process and retry.

You can also manually unlock the state by navigating to the state file in Azure Portal e.g. **resource group > storage account > containers > container** and clicking **break lease**.
