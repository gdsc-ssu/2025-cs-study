## Summary
The modern infrastructure of software services has shifted from on-premise setups to the cloud. Terraform is a tool for managing cloud infrastructure. It is a powerful, open-source Infrastructure as Code (IaC) tool developed by HashiCorp. Terraform can efficiently provision and manage infrastructure resources. In this post, I will explain the basics and practice of using Terraform and why it is important in the modern DevOps environment.
### IaC
Infrastructure as Code(IaC) means provisioning infrastructure not through manual process, but as code. With IaC, a file contains the configuration of the infrastructure, allowing us to edit and deploy resources more easily. IaC ensures the same environment is always provisioned. By codifying and documenting the infrastructure configuration, it supports managing resources to prevent undocumented manual changes.
Version control is one of the crucial feature of Terraform. Like other software source code, Terraform configuration files need version control. Deploying infrastructure as code means dividing configurations into modules and combining them in various ways through automation.
By automating the provisioning of infrastructure, we avoid manually managing configuration for servers, OS, storage, etc. Codifying the infrastructure allows us to create templates that can be used to provision identical environments.
### Terraform
Terraform is a leading open-source infrastructure automation tool that follows the IaC model. Infrastructure automation involves configuring, managing, and modifying infrastructure, including cloud services (AWS, GCP, Azure, etc.) and on-premise environments. With Terraform, managing infrastructure as code allows for version control and repeatable infrastructure setups. Key features of Terraform include:
1. **Declarative Language:** Terraform uses a declarative language to define infrastructure. The developer specifies the desired final state rather than the individual steps to achieve that state. Terraform then compares the configuration with the current state of the infrastructure to identify and apply changes.
2. **Broad Support:** Terraform supports various infrastructure platforms and providers, such as AWS, Azure, GCP, VMware, OpenStack, and Kubernetes.
3. **Reusable Code:** Terraform uses modules to manage configurations. With modules, you can reuse and combine configurations easily.
4. **Plan and Apply:** Terraform creates a plan before applying changes to the infrastructure. The `plan` command checks and validates configurations before they are applied, allowing you to identify potential issues beforehand. If the plan is valid, the `apply` command is used to provision actual resources.
5. **State Management:** Terraform maintains state files to track the current state of the infrastructure. The state file helps Terraform understand the current resources and track changes.
6. **Continuous Updates:** Terraform supports continuous updates and integrations to manage infrastructure automatically using CI/CD pipelines.
With these features, Terraform helps efficiently manage cloud and on-premise infrastructure as code.
### Terraform HCL Syntax
HCL (HashiCorp Configuration Language) is the language used for Terraform configuration files. HCL is designed to be easy to read and maintain.

```
resource "aws_instance" "example" {
  ami = "abc123"

  network_interface {
    # ...
  }
}
```

- **resource:** The type of block, such as `resource`, `data`, `variable`, or `output`.
- **"aws_instance" "example":** The block label, indicating the type of resource and a user-defined name.
- **{}:** The curly braces enclose the configuration for the resource.
#### Terraform Providers
Terraform uses plugins called providers to manage various cloud services, SaaS applications, and other APIs. Providers must be declared in the configuration file and may require specific configurations like an endpoint or region for cloud services.
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = " ~> 4.0"
    }
  }
}

provider "aws" {
  region  = "ap-northeast-2"
  profile = "your_profile"
}
```
In this example, the AWS provider is declared, allowing Terraform to configure various AWS services.
#### Resources and Data Sources
Terraform manages infrastructure using resources and data sources.
1. **Resources:** Resources are the most crucial elements in Terraform. Each resource block defines an infrastructure object, such as a virtual network, computing instance, database, or DNS record.
    ```
    resource "provider_type" "resource_name" {
      attribute1 = value1
      attribute2 = value2
      # ...
    }
    ```
    - For example, to create an EC2 instance with AWS, you can use the following resource block:
    ```
    resource "aws_instance" "example_instance" {
      ami           = "ami-12345678"
      instance_type = "t2.micro"
    }
    ```
    - **aws_instance:** Indicates that an EC2 instance will be created.
	- **example_instance:** The name used in the code.
	- **ami, instance_type:** Attributes specifying the EC2 instance details.
2. **Data Sources:** Data sources allow you to fetch information about existing infrastructure. They are used to retrieve information from external systems, like querying properties of an existing Amazon S3 bucket or network configuration.
    ```
    data "provider_type" "data_source_name" {
      attribute1 = value1
      attribute2 = value2
    }
    ```
    For example, to retrieve details of an existing VPC:
    ```
    data "aws_vpc" "existing_vpc" {
      id = "vpc-12345678"
    }
    
    # vpc의 이름을 출력할 수 있다!
    output "vpc_id" {
      value = data.aws_vpc.existing_vpc.id
    }
    ```
Resources represent the infrastructure components being managed, while data sources are used to query and use information about existing resources.
### Modules
Terraform modules are reusable code components that bundle together multiple resources and data sources to represent a specific functionality or configuration. Using modules helps avoid code duplication and improves readability and maintainability. Once you create a module for a specific functionality, you can easily reuse or share it later.
Modules are actively shared within the Terraform community for various services([Terraform Registry](https://registry.terraform.io/modules/terraform-aws-modules/ecs/aws/latest)). Example of using a module:
```
module "ecs_cluster_dev" {
  source = "../../modules/ecs_cluster"

  project = local.project_name
  environment = local.environment
}
```
- **source:** Specifies the path to the module's infrastructure components, which can be local or from a URL.
- **arguments:** These are the arguments used by the module.
### Variables
Variables in Terraform allow you to parameterize and modularize the configuration. They can be used to pass different values into the configuration during execution.
```
variable "project" {
  type        = string
  description = "Name of this project"
}

variable "environment" {
  type        = string
  description = "service environment"
}
```
Each variable can have a description, default value, and type.
### Local Values
Local values are temporary variables within a Terraform configuration that can store computed values or intermediate results. They are often used to improve code readability and reduce duplication.
```
locals {
  project_name        = "project_name"
  services = ["service1"]

  # Domain name
  root_domain             = "your_domain.com"
  frontend_domain         = "frontend.${local.root_domain}"
  frontend_port           = 3000
  backend_domain          = "api.${local.frontend_domain}"
  backend_port            = 5000

  # Main VPC
  vpc_name              = "${local.project_name}-vpc"
  internet_gateway_name = "${local.project_name}-internet-gateway"
  route_table_name      = "${local.project_name}-route-table"
  nat_gateway_a_name    = "${local.project_name}-nat-gateway-a"
  nat_gateway_c_name    = "${local.project_name}-nat-gateway-c"

  # CIDR blocks # FIXME
  # Refer to README at <https://github.com/AlmSmartDoctor/smart-doctor-common-terraform>
  # Use block in 10.0.144.0/20, 10.0.160.0/20
  vpc_cidr_block       = "10.0.0.0/16"
  public_a_cidr_block                    = "10.0.0.0/24"
  public_c_cidr_block                    = "10.0.1.0/24"

  thirdparty_server_api_cidr_block = ["10.0.4.0/26", "10.0.4.64/26", ]

  # AWS base configuration
  region              = "ap-northeast-2"
  availability_zone_a = "${local.region}a"
  availability_zone_c = "${local.region}c"

  # Health check
  load_balancer_health_check_path = "/"
}
```
Local values can be used for constants or for more complex calculations.
### Output Values
Output values in Terraform are similar to print statements in programming. They are used to display or expose information about resources and data sources after Terraform runs. This information can then be used in other modules or configurations.
```
output "vpc_name" {
  value = data.aws_vpc.existing_vpc.name
}
```
![Pasted image 20240729143838](https://github.com/user-attachments/assets/649da7c0-4363-491a-be83-7b1be718c90e)

Output values help in verifying the values of infrastructure components or passing them to other Terraform configurations.
### State Management
Terraform's state management is crucial for tracking the resources it manages. The state file records information about the resources and their configuration, allowing Terraform to determine the current state of the infrastructure and plan updates accordingly.

In Terraform, the **state** represents the current configuration and status of infrastructure resources. It includes details of changes made, resources created, and relationships between resources. This state allows Terraform to ensure consistency between the actual infrastructure and the defined code, identifying any necessary changes. Key State Files:
1. `terraform.tfstate`: This JSON file stores the current state of the infrastructure managed by Terraform, including resource IDs, configuration values, and status. It can be saved locally or in remote storage.
2. `terraform.tfstate.backup`: A backup of the state file created each time `terraform apply` is run. It helps in recovering previous states if needed.
3. `.terraform.lock.hcl`: Introduced in Terraform 0.14, this file locks the versions of modules and plugins used, ensuring consistent environment setup across different systems.
	![Pasted image 20240729143851](https://github.com/user-attachments/assets/e11605ad-dfa9-4081-b13a-e5b16ce6bf69)

State storage is where the Terraform state file is kept. This can be on a local disk or a remote location like S3 or Terraform Cloud.
- **Local State**: The state file is stored locally on disk, which is suitable for personal projects or small-scale environments.
- **Remote State**: Ideal for collaborative environments, storing the state remotely allows multiple users to work together. 
  ![Pasted image 20240729143911](https://github.com/user-attachments/assets/7c8ed497-3895-48cc-9eb1-aa2845f9c532)

  It includes mechanisms to avoid conflicts, such as locking, which prevents concurrent operations that might lead to inconsistent states.
  
  Example of remote state configuration using S3 and DynamoDB for locking:
	```
	backend "s3" {
	  bucket         = "my-tf-state"
	  key            = "my-key"
	  region         = "ap-northeast-2"
	  dynamodb_table = "my-tf"
	  profile = "profile"
	}
	```
	![Pasted image 20240729143922](https://github.com/user-attachments/assets/6511d87f-c5cc-4570-8ff6-d1d7d9f8bb3b)


**State Management** involves handling the state file and keeping track of changes. Terraform uses the state file to compare the current infrastructure with the desired configuration defined in the code. Basic Workflow:
- Write and define resources in the code.
- `terraform init`: Initializes the project, installs modules, and configures the state storage.
	![Pasted image 20240729143935](https://github.com/user-attachments/assets/1b56c246-5a34-4034-b7f2-99b7e33493a2)

- `terraform plan`: Creates an execution plan to show what changes will be made to match the desired state.
	![Pasted image 20240729143943](https://github.com/user-attachments/assets/edeb1773-0030-4988-9de2-9cafd96ee802)

- `terraform apply`: Applies the planned changes to update the infrastructure.
	![Pasted image 20240729143951](https://github.com/user-attachments/assets/eab40bf7-4c97-4400-8a4e-b60df7828854)

- The state file is updated to reflect these changes.
- Repeat the `plan` and `apply` cycle as needed for ongoing changes.
State management is a key feature of Terraform, ensuring that infrastructure remains consistent with the code and stable over time. Proper management and protection of the state file are crucial to avoid discrepancies and potential issues in infrastructure management.
### Conclusion
![Pasted image 20240729155116](https://github.com/user-attachments/assets/da5ce17c-eb8b-4e09-a146-5ddfa878ce4c)

This post has explored how Terraform enables Infrastructure as Code (IaC) for efficient infrastructure management. Despite its learning curve, Terraform's ability to automate and simplify infrastructure management is compelling. Utilizing Terraform for managing multiple cloud services can greatly enhance efficiency and consistency. Additionally, Terraform's official documentation is an excellent resource for learning and troubleshooting. I hope this post helps you to configure infrastructure.
- [Files and Directories - Configuration Language | Terraform | HashiCorp Developer](https://developer.hashicorp.com/terraform/language/files)
- [Terraform Registry](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
