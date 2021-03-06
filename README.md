# terraform-example

___NOTE: More about this and related repos can be found here: https://blog.threatstack.com/incorporating-aws-security-best-practices-into-terraform-design___

___This repository is meant to be copied and provide people a starter for a greenfield environment.  The Terraform service (terraform-example-*) and Terraform module (tf_example*) repositories are opinionated.  (ex., see [tf_example_aws_iam_s3](https://github.com/threatstack/tf_example_aws_s3) and how it automatically handles bucket naming to avoid name collisions.)  They are opinionated because all infrastructure is opinionated in certain ways.  If these repositories prove useful to you, fork or copy them and start your own environment.___

___Not all modules are complete.  Features are added as needed and new problems are solved as they're encountered.___

The root of the Example.com infrastructure Terraform.

This repository is the root repository for Terraform.  It's role is to establish the remote state bucket and setup the logging for it so we can track state file changes.

## Terraform Structure
Our Terraform design assumes all Terraform code should live as close to the service that it manages as possible.  Additionally we want the infrastructure to be as open and accessible as possible but still limit access to anything we deem sensitive; that could take the form of private repos or restricting write access.  To accomplish this we separate our concerns into individual repositories.

We have two types of Terraform repositories:
* Module repositories
* Service repositories

Additionally Terrafrom code may live within an application code repository.

### Module Repositories
Terraform modules describe how to build infrastructure.

Module repositories are for Terraform modules that will be reused.  They cannot by themself deploy a service.  Instead they are consumed by service repositories.  Their job is to define a repeatable design to follow.  These modules define a group or resoruces and how they interact with one another and/or conventions such as naming of resources.  These modules are opinionated but flexible ro prevent the need to create a duplicate module.

### Service Repositories
Terraform service define what infrastructure looks like.

Service repositories are for managing pieces of our infrastructure.  They are typically foundational blocks that don't correspond to a particular application.  For example:
* [AWS VPC](https://github.com/threatstack/terraform-exmple-aws-vpc)
* [AWS IAM](https://github.com/threatstack/terraform-example-aws-iam)
  * Additional users, groups, roles, and policies directly associated with services can be added using these modules:
    * [tf_example_aws_iam](https://github.com/threatstack/tf_example_aws_iam_user)
    * tf_example_aws_iam_role
    * tf_example_aws_iam_role
    * tf_example_aws_iam_policy
* [AWS CloudTrail](https://github.com/threatstack/terraform-example-aws-cloudtrail)
  * Additional cloustrails can be created with:
    * [tf_example_aws_iam_cloudtrail](https://github.com/threatstack/tf_example_aws_cloudtrail)
* AWS Route53
  * This should handle zone and basic record setup.
  * Service related recorss records should be handled by:
    * tf_example_aws_route53_record
* Bastion host
  * This is the only SSH ingress point into the enviornment

For services with code assets, add the terraform code for the service under _deploy/_.  For services without code assets, create a repo called terraform-example-[service_name].  Additional services should use the Terraform module below.
* [tf_example_svc](https://github.com/threatstack/tf_example_svc)

___Note: We are currently mixing modules into our service repos.  We should probably stop that and move our modules into their own repos for consistency.___

### Application Repositories
Application repositories define what a particular application should look like when deployed.

Taerraform code should live as close to the application code that is to be managed.  It should live under the ___deploy/___ directory of the repository.

