# Day 100

## Task

The Nautilus DevOps team has been tasked with setting up an EC2 instance for their application. To ensure the application performs optimally, they also need to create a CloudWatch alarm to monitor the instance's CPU utilization. The alarm should trigger if the CPU utilization exceeds 90% for one consecutive 5-minute period. To send notifications, use the SNS topic named devops-sns-topic, which is already created.

    Launch EC2 Instance: Create an EC2 instance named devops-ec2 using any appropriate Ubuntu AMI (you can use AMI ami-0c02fb55956c7d316).

    Create CloudWatch Alarm: Create a CloudWatch alarm named devops-alarm with the following specifications:
        Statistic: Average
        Metric: CPU Utilization
        Threshold: >= 90% for 1 consecutive 5-minute period
        Alarm Actions: Send a notification to the devops-sns-topic SNS topic.

    Update the main.tf file (do not create a separate .tf file) to create a EC2 Instance and CloudWatch Alarm.

    Create an outputs.tf file to output the following values:

    KKE_instance_name for the EC2 instance name.
    KKE_alarm_name for the CloudWatch alarm name.

Notes:

    The Terraform working directory is /home/bob/terraform.

    Right-click under the EXPLORER section in VS Code and select Open in Integrated Terminal to launch the terminal.

    Before submitting the task, ensure that terraform plan returns No changes. Your infrastructure matches the configuration.

## Solution

No variables this time, we're just adding to `main.tf` and creating `outputs.tf`.  The SNS topic is already declared as a resource in `main.tf`:

```hcl
resource "aws_sns_topic" "sns_topic" {
  name = "devops-sns-topic"
}
```

So let's just add the EC2 instance and create the alarm:

```hcl
resource "aws_instance" "devops_ec2" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"

  tags = {
    Name = "devops-ec2"
  }
}

resource "aws_cloudwatch_metric_alarm" "devops_alarm" {
  alarm_name          = "devops-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = 1
  period              = 300
  threshold           = 90
  statistic           = "Average"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"

  dimensions = {
    InstanceId = aws_instance.devops_ec2.id
  }

  alarm_actions = [aws_sns_topic.sns_topic.arn]
}
```

And then create `outputs.tf` and add:

```hcl
output "KKE_instance_name" {
  value = aws_instance.devops_ec2.tags.Name
}

output "KKE_alarm_name" {
  value = aws_cloudwatch_metric_alarm.devops_alarm.alarm_name
}
```

Then the usual workflow:

```bash
terraform init
terraform plan
terraform apply
```

The outputs from `terraform apply` should print:

```
KKE_alarm_name = "devops-alarm"
KKE_instance_name = "devops-ec2"
```

## Validation

```bash
terraform state list
terraform plan
```

`terraform state list` should show the SNS topic, the instance, and the alarm, and `terraform plan` should report no changes.

## Insights

The SNS topic was already declared as a resource in the starter `main.tf`, so all we had to do was reference it in the alarm's `alarm_actions`.  This task actually provided an AMI to use, but didn't specify a EC2 type, so I just went with `t2.micro`.

As for the alarm thresholds, all we really had to do was input the specifications into the right arguments.  So ">= 90%" is `GreaterThanOrEqualToThreshold` with `threshold = 90`, and "1 consecutive 5-minute period" is `evaluation_periods = 1` with `period = 300` meaning 300 seconds, or 5 minutes.  The `InstanceId` dimensions ties the alarm to this specific instance, and then we just use the pre-created sns topic for the `alarm_actions`.

And with that, we're done with 100 days of DevOps.
