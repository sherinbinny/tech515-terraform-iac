# IaC

- [IaC](#iac)
  - [What problem needs solving?](#what-problem-needs-solving)
  - [What is "provisioning" servers?](#what-is-provisioning-servers)
  - [What have we automated so far?](#what-have-we-automated-so-far)
  - [How does IaC help?](#how-does-iac-help)
  - [Solving the problem with IaC](#solving-the-problem-with-iac)
  - [What is IaC?](#what-is-iac)
  - [Benefits of IaC?](#benefits-of-iac)
  - [Tools available for IaC](#tools-available-for-iac)
  - [Links to Terraform and Ansible doc](#links-to-terraform-and-ansible-doc)

<br>

## What problem needs solving?

- At moment, still manually "provisioning" servers

<br>

## What is "provisioning" servers?

- The process of setting up and configuring servers
- The process typically involves:
  1. Creating the server instance
  2. Configure the OS + software e.g.
     1. setting up users + permissions
     2. configuring the networking
     3. necessary software packages
  3. Deploy the application
  4. Configure monitoring & logging

<br>

## What have we automated so far?

- VMs
  - Creation of VMs?
    - Somewhat through AWS Scaling Groups for multiple VMs
    - Launch templates have helps to speed up manual deploy of a single VM
    - Creation of the infrastructure they live in? NO
    - Setup and configuring of teh software on them?
      - Bash scripts
      - User data or startup scripts
      - AMIs

What if we could automate ALL of it?

<br>

## How does IaC help?

- Codify all of it
- We do not need to define the steps (different to Bash scripts which are imperative)
- We will define the desired state/outcomes - that is called declarative (the tool will work out the steps)

<br>

## Solving the problem with IaC

Infrastructure as Code (IaC) can do all the provisioning of:

- the infrastructure itself (the servers, the networking)
- configuring the servers (installing the software, configuring the settings)

<br>

## What is IaC?

- A way to mange & provision computers through a machine-readable definition of infrastructure

<br>

## Benefits of IaC?

- Speed and simplicity
- Consistency and accuracy
- Version control
- Scalable

<br>

## Tools available for IaC

2 types of IaC tools:
* Configuration management tools
  * They install/configure software (after the infrastructure has been created)
  * Examples:
    * Chef
    * Puppet
    * Ansible
* Orchestration tools
  * Manage/orchestrate infrastructure
  * Examples:
    * CloudFormation (AWS)
    * Hashicorp Terraform
    * ARM/Bicep templates (Azure)
    * Ansible (can do this, but best used as a configuration management tool)

## Links to Terraform and Ansible doc

- Link to [Terraform doc](README.md)
- Link to Ansible doc
