# Terraform and State on AWS

I've seen some confusion and misconceptions about how this work, so I decided to write this up.

## Introduction

Terraform is an open-source infrastructure as code (IAC) tool that enables users to define, deploy and manage cloud infrastructure resources in a declarative way. One of the key features of Terraform is the ability to store the current state of infrastructure resources, which allows Terraform to manage changes to these resources over time.

In this readme, we will discuss how Terraform works with state on AWS, including how to configure and manage the state of infrastructure resources.
## Terraform State

The state of infrastructure resources managed by Terraform is stored in a file called the state file. This file keeps track of the current state of the resources, including information such as the resource type, ID, and any associated metadata.

By default, Terraform stores the state file locally on the machine where Terraform is run. However, this can cause issues when working in a team or across multiple environments. To address this, Terraform supports remote state storage, allowing teams to share and collaborate on infrastructure resources.
## Remote State Storage

Remote state storage allows Terraform to store the state file in a remote location, such as an Amazon S3 bucket or a HashiCorp Consul cluster. This allows multiple users to work on the same infrastructure resources, without the risk of overwriting or conflicting changes.

To configure remote state storage on AWS, you can use the terraform block in your configuration file, as shown below:

```terraform

terraform {
  backend "s3" {
    bucket = "example-bucket"
    key    = "terraform.tfstate"
    region = "us-east-1"
  }
}
```
This example configures Terraform to store the state file in an S3 bucket named example-bucket, with the key terraform.tfstate. The region parameter specifies the AWS region where the bucket is located.
## Locking State

When multiple users are working with the same state file, there is a risk of conflicts and overwriting changes. To prevent this, Terraform supports state locking, which allows users to lock the state file while they are making changes. This prevents other users from making changes to the same resources at the same time.

To enable state locking on AWS, you can use the dynamodb backend, which provides a locking mechanism using an Amazon DynamoDB table. To configure state locking with DynamoDB, you can modify the terraform block in your configuration file as follows:
```terraform

terraform {
  backend "s3" {
    bucket = "example-bucket"
    key    = "terraform.tfstate"
    region = "us-east-1"
    dynamodb_table = "example-lock-table"
  }
}
```
This example adds the dynamodb_table parameter to the backend configuration, which specifies the name of the DynamoDB table to use for state locking.

## Flow example

```
  +---------------------+      +---------------+
  |                     |      |               |
  |  Terraform State    +<-----+  AWS Resources|
  |                     |      |               |
  +---------------------+      +---------------+

               |
               |
               V

        +----------------+
        |                |
        | Terraform Code |
        |                |
        +----------------+
```

Explanation:

  *  Terraform state is a snapshot of the current state of your infrastructure managed by Terraform.
  *  AWS resources are the actual resources created and managed by AWS.
  *  Terraform code is the set of instructions that Terraform follows to create, update or delete AWS resources.
  *  The flowchart shows that Terraform state interacts with AWS resources, meaning that it can read and modify the state of those resources as needed.
  *  Terraform code is used to create or modify AWS resources, and it does so by using the Terraform state to determine what changes need to be made.

## Conclusion

Terraform state is a powerful feature that allows users to manage infrastructure resources in a declarative way. By using remote state storage and state locking, teams can collaborate on infrastructure resources with confidence, knowing that their changes will not conflict with others. With the examples provided in this readme, you should be able to configure Terraform to work with state on AWS and start managing infrastructure resources with confidence.
