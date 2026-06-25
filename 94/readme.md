# Day 94

## Task

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the AWS cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations. By breaking down the migration into smaller tasks, the Nautilus DevOps team can systematically progress through each stage, allowing for better control, risk mitigation, and optimization of resources throughout the migration process.

Create a VPC named xfusion-vpc in region us-east-1 with any IPv4 CIDR block through terraform.

The Terraform working directory is /home/bob/terraform. Create the main.tf file (do not create a different .tf file) to accomplish this task.

Note: Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.

## Solution

Looking at the `provider.tf` we've already got the `region` set to `us-east-1` so we just need to make the actual vpc resource.  We need to make `main.tf` and then add:

```hcl
resource "aws_vpc" "xfusion_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "xfusion-vpc"
  }
}
```

> the `cidr_block` doesn't matter so I just went with a simple usual one

Then it's the usual Terraform workflow:

```bash
terraform init
terraform plan
terraform apply
```

## Validation

Confirm Terraform thinks everything is created:

```bash
terraform state list
terraform show
```

We should see `aws_vpc.xfusion_vpc` in the state.

We can also check directly with the AWS CLI:

## Insights

Ok so we're in to Terraform.  It looks like it's using VS Code to do this, which makes things a bit different than previous tasks, but still easy.

Nothing tricky here, just using an `aws_vpc` block to create a vpc.  The `provider.tf` is already set up so we didn't need to do any configuration here.  Easy start to the Terraform chapter.
