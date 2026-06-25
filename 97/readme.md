# Day 97

## Task

When establishing infrastructure on the AWS cloud, Identity and Access Management (IAM) is among the first and most critical services to configure. IAM facilitates the creation and management of user accounts, groups, roles, policies, and other access controls. The Nautilus DevOps team is currently in the process of configuring these resources and has outlined the following requirements.

Create an IAM policy named iampolicy_yousuf in us-east-1 region using Terraform. It must allow read-only access to the EC2 console, i.e., this policy must allow users to view all instances, AMIs, and snapshots in the Amazon EC2 console.

The Terraform working directory is /home/bob/terraform. Create the main.tf file (do not create a different .tf file) to accomplish this task.

Note: Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.

## Solution

We're just going to use the predefined `ec2:Describe` for this policy.

Create `main.tf`:

```hcl
resource "aws_iam_policy" "iampolicy_yousuf" {
  name        = "iampolicy_yousuf"
  description = "read-only access to the EC2 console"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action   = "ec2:Describe*"
        Effect   = "Allow"
        Resource = "*"
      }
    ]
  })
}
```

Then the usual:

```bash
terraform init
terraform plan
terraform apply
```

## Validation

```bash
terraform state list
terraform show
```

We should see `aws_iam_policy.iampolicy_yousuf` in the state and the details from `terraform show` like previous tasks

## Insights

All we really needed for this IAM policy was `ec2:Describe`, which is exactly what's in the Terraform documentation page for `aws_iam_policy` resources.  So the only thing we needed to set was the name.

Have I mentioned that I like terraform?  Because I kind of like terraform...