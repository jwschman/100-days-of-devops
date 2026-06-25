# Day 96

## Task

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the AWS cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units.

For this task, create an EC2 instance using Terraform with the following requirements:

    The EC2 instance must use the value nautilus-ec2 as its Name tag, which defines the instance name in AWS.

    Use the Amazon Linux ami-0c101f26f147fa7fd to launch this instance.

    The Instance type must be t2.micro.

    Create a new RSA key named nautilus-kp.

    Attach the default (available by default) security group.

    The Terraform working directory is /home/bob/terraform. Create the main.tf file (do not create a different .tf file) to provision the instance.

    Note: Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.


## Solution

So we're going to need to create a `tls_private_key` before actually creating the `aws_key_pair`.  

We're also going to use a data block for the default security like we did with the default vpc previously.  We could probably just omit it, but actually setting it may satisfy the task better.

Let's get it all by creating `main.tf`:

```hcl
resource "tls_private_key" "nautilus_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "nautilus_kp" {
  key_name   = "nautilus-kp"
  public_key = tls_private_key.nautilus_key.public_key_openssh
}

data "aws_security_group" "default" {
  name = "default"
}

resource "aws_instance" "nautilus_ec2" {
  tags = {
    Name = "nautilus-ec2"
  }

  ami                    = "ami-0c101f26f147fa7fd"

  instance_type          = "t2.micro"
  key_name               = aws_key_pair.nautilus_kp.key_name
  vpc_security_group_ids = [data.aws_security_group.default.id]

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

We should see `aws_instance.nautilus_ec2`, `aws_key_pair.nautilus_kp` and `tls_private_key` in the state, and their details from `terraform show`

## Insights

The only thing really of note here is that to use `aws_key_pair` you first have to create the keypair using `tls_private_key`, but once that's created you can reference it inside the `aws_key_pair`.  Everything else was once again just reading the documentation to get the right arguments.
