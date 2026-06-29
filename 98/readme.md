# Day 98

## Task

The Nautilus DevOps team is expanding their AWS infrastructure and requires the setup of a private Virtual Private Cloud (VPC) along with a subnet. This VPC and subnet configuration will ensure that resources deployed within them remain isolated from external networks and can only communicate within the VPC. Additionally, the team needs to provision an EC2 instance under the newly created private VPC. This instance should be accessible only from within the VPC, allowing for secure communication and resource management within the AWS environment.

    Create a VPC named nautilus-priv-vpc with the CIDR block 10.0.0.0/16.

    Create a subnet named nautilus-priv-subnet inside the VPC with the CIDR block 10.0.1.0/24 and auto-assign IP option must not be enabled.

    Create an EC2 instance named nautilus-priv-ec2 inside the subnet and instance type must be t2.micro.

    Ensure the security group of the EC2 instance allows access only from within the VPC's CIDR block.

    Create the main.tf file (do not create a separate .tf file) to provision the VPC, subnet and EC2 instance.

    Use variables.tf file with the following variable names:
        KKE_VPC_CIDR for the VPC CIDR block.
        KKE_SUBNET_CIDR for the subnet CIDR block.

    Use the outputs.tf file with the following variable names:
        KKE_vpc_name for the name of the VPC.
        KKE_subnet_name for the name of the subnet.
        KKE_ec2_private for the name of the EC2 instance.


Notes:

    The Terraform working directory is /home/bob/terraform.

    Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.

    Before submitting the task, ensure that terraform plan returns No changes. Your infrastructure matches the configuration.

## Solution

So we need to make three different files.  Let's make `variables.tf` first because we'll reference it in `main.tf` later.  All we need to do is add two different CIDR variables with a default set to what the task described.

```hcl
variable "KKE_VPC_CIDR" {
  default = "10.0.0.0/16"
}

variable "KKE_SUBNET_CIDR" {
  default = "10.0.1.0/24"
}
```

Then we can just get to work on `main.tf`.  We need to make sure `map_public_ip_on_launch = false` is set in the subnet just since the task calls it out.  We also need an AMI for the EC2 instance, so I just hard-coded the latest version of ubuntu on us-east-1.

```hcl
resource "aws_vpc" "nautilus_priv_vpc" {
  cidr_block = var.KKE_VPC_CIDR

  tags = {
    Name = "nautilus-priv-vpc"
  }
}

resource "aws_subnet" "nautilus_priv_subnet" {
  vpc_id                  = aws_vpc.nautilus_priv_vpc.id
  cidr_block              = var.KKE_SUBNET_CIDR
  map_public_ip_on_launch = false

  tags = {
    Name = "nautilus-priv-subnet"
  }
}

resource "aws_security_group" "nautilus_priv_sg" {
  vpc_id = aws_vpc.nautilus_priv_vpc.id

  ingress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }
}

resource "aws_instance" "nautilus_priv_ec2" {
  ami                    = "ami-0720aff18b7589fbd"
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.nautilus_priv_subnet.id
  vpc_security_group_ids = [aws_security_group.nautilus_priv_sg.id]

  tags = {
    Name = "nautilus-priv-ec2"
  }
}
```

And finally we just make the `outputs.tf`:

```hcl
output "KKE_vpc_name" {
  value = aws_vpc.nautilus_priv_vpc.tags.Name
}

output "KKE_subnet_name" {
  value = aws_subnet.nautilus_priv_subnet.tags.Name
}

output "KKE_ec2_private" {
  value = aws_instance.nautilus_priv_ec2.tags.Name
}
```

Then the terraform workflow:

```bash
terraform init
terraform plan
terraform apply
```

The outputs from `terraform apply` should print:

```
KKE_ec2_private = "nautilus-priv-ec2"
KKE_subnet_name = "nautilus-priv-subnet"
KKE_vpc_name = "nautilus-priv-vpc"
```

## Validation

```bash
terraform state list
terraform plan
```

The `terraform state list` should show us the four resources we made, and `terraform plan` should show that no differences were found.

## Insights

This was the most detailed Terraform task so far, but still quite simple.  The only real difference here was using outputs and variables, which are both basics of using Terraform.

Wiring up the resources to the VPC was simple and we just have to reference the VPC ID in the resources.

Also, since no AMI was specified I just decided to hardcode in an Ubuntu image rather than setting something in a variable or data block.
