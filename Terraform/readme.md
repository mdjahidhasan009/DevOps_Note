## What is IaC?

It enables you to define, provision, and manage infrastructure across multiple cloud providers using a **declarative 
configuration language**.

It supports **multi-cloud environments**, allowing **consistent and efficient infrastructure management**.

### Terraform Code Example

This example shows how Terraform code answers key infrastructure questions:
*   **Which cloud provider?** -> The `required_providers` block.
*   **Which location?** -> The `provider` block specifying the region.
*   **What type of resource?** -> The `resource` block defining an `aws_instance`.

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "5.54.1"
    }
  }
}

provider "aws" {
  region = var.region
}

resource "aws_instance" "myserver" {
  ami           = "ami-0c0e147c7063bd7"
  instance_type = "t3.nano"

  tags = {
    Name = "SampleServer"
  }
}
```

## Terraform Config

*   It uses **.tf** extension
*   Format is HCL (Hashicorp Config Language)
*   Declarative Language

### Core Terraform Commands

*   `terraform init`
*   `terraform plan`
*   `terraform apply/destroy`

### Key Benefits

*   **"Consistency:** Define infrastructure in code"
*   **"Automation:** Reduce manual work"
*   **"Repeatable:** Replicate environments easily"

## State Management

The state file (**terraform.tfstate**) maintains a detailed record of the current state of managed resources.

This state file can be stored locally or remotely, with remote storage options enabling collaboration by sharing the
state across teams and environments.

### Multiple Resources using

*   `Count`
*   `for_each`

## Terraform Modules:

Modules are containers for multiple resources that are used together.

A module consists of a collection of **.tf and/or .tf.json** files kept together in a directory.

Modules are the main way to package and reuse resource configurations with Terraform.

### Terraform Workspaces

A single `tf config` directory can be used to manage multiple, distinct environments. Each workspace (e.g.,
`workspace-dev`, `workspace-test`, `workspace-prod`) has its own separate state file (`tfstate`), allowing you to manage
different sets of infrastructure with the same configuration files.

## Terraform Cloud

Terraform Cloud is a managed service provided by HashiCorp that facilitates collaboration on Terraform configurations.

**Providing features like**
* remote state management,
* version control system (VCS) integration,
* automated runs, and
* secure variable management.

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)