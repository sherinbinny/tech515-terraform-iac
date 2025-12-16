# AWS Credentials Setup & Terraform Verification

This guide explains how to **permanently set up AWS credentials** on a Mac and verify that Terraform can access your AWS account.

---

## **Prerequisites**

- MacOS machine
- Homebrew installed
- Terraform installed (`terraform --version` to check)
- AWS account credentials (Access Key ID & Secret Access Key) from the CSV file provided

---

## **Step 1: Install AWS CLI**

Check if AWS CLI is installed:

```bash
aws --version
```

If not installed, install via Homebrew:

```bash
brew install awscli
```

Verify installation:

```bash
aws --version
```

---

## **Step 2: Configure AWS CLI**

Run the following command:

```bash
aws configure
```

You will be prompted for:

```
AWS Access Key ID [None]: <paste your AWS_ACCESS_KEY_ID from CSV>
AWS Secret Access Key [None]: <paste your AWS_SECRET_ACCESS_KEY from CSV>
Default region name [None]: eu-west-1  # or your preferred region
Default output format [None]: json
```

This stores your credentials in `~/.aws/credentials` and region in `~/.aws/config`. Terraform will automatically use this profile.

---

## **Step 3: Verify AWS Credentials**

Run:

```bash
aws sts get-caller-identity
```

You should see your AWS **account ID**, **user ARN**, and **account number**. This confirms that AWS CLI can access your account.

---

## **Step 4: Create Terraform Test Folder**

Create a folder for testing:

```bash
mkdir ~/terraform-test
cd ~/terraform-test
```

---

## **Step 5: Create Terraform Configuration**

Create a file named `main.tf`:

```bash
nano main.tf
```

Paste the following:

```hcl
provider "aws" {
  region = "eu-west-1"
}

data "aws_caller_identity" "current" {}

output "aws_account_id" {
  value = data.aws_caller_identity.current.account_id
}
```

Save and exit (`Ctrl+O` → Enter → `Ctrl+X`).

---

## **Step 6: Initialize Terraform**

Run:

```bash
terraform init
```

This downloads the required AWS provider.

---

## **Step 7: Apply Terraform Configuration**

Run:

```bash
terraform apply -auto-approve
```

Output will show your AWS **account ID**:

```
aws_account_id = "123456789012"
```

✅ This confirms Terraform can access your AWS account using the configured credentials.

---

## **Notes**

- Using **AWS CLI profiles** is the recommended method for storing credentials instead of manually adding environment variables.
- Terraform will automatically detect AWS credentials stored in `~/.aws/credentials`.
- Always keep your AWS credentials secure and never share them.
