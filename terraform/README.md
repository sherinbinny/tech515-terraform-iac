# Intro to Terraform

## Steps to install

```
  gh repo create terraform-iac --public --source=. --remote=origin --push\n
  git remote -v
  git remote remove origin\n
  git remote -v\n
  git remote add origin https://github.com/sherinbinny/terraform-iac.git
  git remote -v\n
  git push -u origin main\n
  git pull
  brew tap hashicorp/tap
  brew install hashicorp/tap/terraform
```
 
## What is Terraform? What is it used for?

* For orchestration
* Different to configuration management tools like Ansible, which deploy software onto existing servers
* Sees infrastructure as immutable (i.e. displosable), while CM tools see infrastructure as mutable/reusable (want to change or update existing servers)
* Uses Hashicorp Configuration Language (HCL) - tries to balance human + machine readability

<br>
 
## Why use Terraform? The benefits?

* Deploys infrastructure, not just applications
* Easy to use
* Sort-of open-source
  * In 2023, started to use Business Source License (BSL)
  * Many organisations prefer to use OpenTofu because it remains open-source
    * community-driven, Linux Foundation-hosted fork of Terraform
    * aims to be a drop-in replacement, maintains compatibility with Terraform state/modules
* Declarative - say what you want, not how to do it
* Cloud-agnostic - deploy to any cloud
  * Use different providers (which are like plugins) to deploy to particular cloud providers
  * Expressive & extensible

<br>

## Alternatives to Terraform

* Pulumi
* AWS CloudFormation, GCP Deployment Manager, Azure Resource Manager (ARM)
  * cloud-specific rather than cloud-agnostic

<br>
 
## Who is using Terraform in the industry?

* Tech Companies and Startups:
Uber, Spotify, Airbnb, Coinbase
 
* Financial Institutions (regulated industry):
JPMorgan Chase, Goldman Sachs, Capital One
 
* Cloud Providers and SaaS Platforms:
AWS, Google Cloud, Salesforce
 
* Media and Entertainment:
The New York Times, Netflix
 
* Healthcare (regulated industry) and Life Sciences:
Philips, Cerner
 
* Telecommunications:
Verizon, T-Mobile
 
* Retail and E-Commerce:
Walmart, Target
 
* Gaming Industry:
Electronic Arts (EA), Riot Games (they make/run League of Legends)
 
* Government and Public Sector:
UK Government Digital Service (GDS), NASA
 
* Consulting and Cloud Services:
Accenture, Deloitte
 
* Education and Research Institutions:
Harvard University, MIT

<br>

## In IaC, what is orchestration? How does Terraform act as "orchestrator"?

* process of automating + managing infrastructure

## How does Terraform act as "orchestrator"?

* Does just creating/provision infrastructure, also...
* Co-ordinating piece of infrastructure to work together, which includes...
  * Setting things up (or destroying) in the right order
  * Connects resources together properly

<br>

## Best practice supplying AWS credentials to Terraform

Order in which Terraform looks up AWS credentials:
  1. Environment variables: AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY (OKAY TO DO THIS IF USING LOCAL PC)
  2. Terraform variables (get them from your code) - should NOT do this - credentials should NOT be hardcoded
  3. AWS CLI and the "aws configure (OKAY TO DO THIS IF USING LOCAL PC)
  4. EC2 instance metadata - you assign an IAM (Identity Access Management) role to the EC2 instance where you are running your terraform commands (BEST PRACTICE)

<br>

## Why use Terraform for different environments (e.g. production, testing, etc)

* Production
  * Easily modify dev/test/other env to be larger scale
* Dev and Testing
  * Quick, reproducable deployments
    * Devs can quickly spinup a testing env that mirrors production
    * Very quickly tear it down after testing (saving money)
