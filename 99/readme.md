# Day 99

## Task

The DevOps team has been tasked with creating a secure DynamoDB table and enforcing fine-grained access control using IAM. This setup will allow secure and restricted access to the table from trusted AWS services only.

As a member of the Nautilus DevOps Team, your task is to perform the following using Terraform:

    Create a DynamoDB Table: Create a table named xfusion-table with minimal configuration.

    Create an IAM Role: Create an IAM role named xfusion-role that will be allowed to access the table.

    Create an IAM Policy: Create a policy named xfusion-readonly-policy that should grant read-only access (GetItem, Scan, Query) to the specific DynamoDB table and attach it to the role.

    Create the main.tf file (do not create a separate .tf file) to provision the table, role, and policy.

    Create the variables.tf file with the following variables:
        KKE_TABLE_NAME: name of the DynamoDB table
        KKE_ROLE_NAME: name of the IAM role
        KKE_POLICY_NAME: name of the IAM policy

    Create the outputs.tf file with the following outputs:
        kke_dynamodb_table: name of the DynamoDB table
        kke_iam_role_name: name of the IAM role
        kke_iam_policy_name: name of the IAM policy

    Define the actual values for these variables in the terraform.tfvars file.

    Ensure that the IAM policy allows only read access and restricts it to the specific DynamoDB table created.

Notes:

    The Terraform working directory is /home/bob/terraform.

    Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.

    Before submitting the task, ensure that terraform plan returns No changes. Your infrastructure matches the configuration.

## Solution

Well this time we're using a `terraform.tfvars` for the values, so first we need to declare the variables in `variables.tf`:

```hcl
variable "KKE_TABLE_NAME" {}
variable "KKE_ROLE_NAME" {}
variable "KKE_POLICY_NAME" {}
```

And set the values in `terraform.tfvars`:

```hcl
KKE_TABLE_NAME  = "xfusion-table"
KKE_ROLE_NAME   = "xfusion-role"
KKE_POLICY_NAME = "xfusion-readonly-policy"
```

Then we just need to add the resources in `main.tf`.

```hcl
resource "aws_dynamodb_table" "xfusion_table" {
  name         = var.KKE_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "UserId"

  attribute {
    name = "UserId"
    type = "S"
  }
}

resource "aws_iam_role" "xfusion_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action    = "sts:AssumeRole"
        Effect    = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      }
    ]
  })
}

resource "aws_iam_policy" "xfusion_readonly_policy" {
  name = var.KKE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "dynamodb:GetItem",
          "dynamodb:Scan",
          "dynamodb:Query"
        ]
        Resource = aws_dynamodb_table.xfusion_table.arn
      }
    ]
  })
}

resource "aws_iam_role_policy_attachment" "xfusion_attach" {
  role       = aws_iam_role.xfusion_role.name
  policy_arn = aws_iam_policy.xfusion_readonly_policy.arn
}
```

And then we can make `outputs.tf`:

```hcl
output "kke_dynamodb_table" {
  value = aws_dynamodb_table.xfusion_table.name
}

output "kke_iam_role_name" {
  value = aws_iam_role.xfusion_role.name
}

output "kke_iam_policy_name" {
  value = aws_iam_policy.xfusion_readonly_policy.name
}
```

Then the same as always:

```bash
terraform init
terraform plan
terraform apply
```

The outputs from `terraform apply` should show:

```
kke_dynamodb_table = "xfusion-table"
kke_iam_policy_name = "xfusion-readonly-policy"
kke_iam_role_name = "xfusion-role"
```

## Validation

```bash
terraform state list
terraform plan
```

The `terraform state list` should show the four resources, and `terraform plan` should report no changes like before.

## Insights

The only real different thing here was that we used a `terraform.tfvars` to set the values rather than using defaults like we did in day 98.  It's how I actually handle things in my Terraform infrastructure and probably the more real-world way of doing things.

Also, since we made an IAM role and policy, we need to also make sure we have the binding to get everything wired together.
