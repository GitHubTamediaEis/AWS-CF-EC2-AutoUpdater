# AWS-CF-EC2-Autoupdater
Create tag based maintenance windows to update EC2 instances.

# AWS-CF-EC2-Autoupdater.yaml

This file is a CloudFormation template that creates the maintenance window.

# Instructions

The solution is available in the [Service Catalog](https://docs.aws.amazon.com/servicecatalog/latest/userguide/end-user-console.html) of your account and can easily be deployed from there.

**Make sure your instances meet the SSM Prerequisites, see section [Instance considerations](#instance-considerations)**

Upon deployment you have to specify custom parameters which are explained hereafter.

# Custom parameters

All parameters except *Description* are required.

| Parameter | Default | Possible values | Notes |
| ------ | ------ | ------ | ------ |
|Name | | string | Name of the maintenance window |
|Description | | string | Description of the maintenance window (optional) |
|Schedule | cron(0 1 ? \* satL \*) | Any cron or rate expression | See section [Schedule considerations](#schedule-considerations) |
|Duration | 2 | 1 - 24 | The duration of the maintenance window |
|Cutoff | 1 | 0 - 23 | Time to stop starting scheduled tasks before maintenance window ends |
|Timezone | Europe/Zurich | Any timezone in [IANA format](https://www.iana.org/time-zones) | Timezone to use for the maintenance window |
|MaxConcurrency | 1 | Any positive number | Number of targets on which to execute tasks at the same time |
|MaxErrors | 0 | 0 - any positive number | Stop tasks after it failed on the specified number of targets |
|TagName | landscape | string | Tag name (key) for instances to include as targets in the maintenance window |
|TagValues | dev,int | CommaDelimitedList | Tag value(s) for instances to include as targets in the maintenance window |

# Tasks

The maintenance window executes the following [Run Commands](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) in the order given:

1) [AWS-UpdateSSMAgent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-plugins.html#aws-updatessmagent)
2) [AWS-RunPatchBaseline](https://docs.aws.amazon.com/systems-manager/latest/userguide/patch-manager-about-aws-runpatchbaseline.html) with parameter Operation set to Install

# Schedule considerations

The schedule must be defined using a [Schedule Expression](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html). It's highly recommended to first test your expression for validity. You can do so by entering your expression as an Event Source in a [CloudWatch Rule](https://console.aws.amazon.com/cloudwatch/home#rules:action=create) (Click *Schedule* and *Cron expression*, then enter your expression without *cron()*. The 10 next resulting trigger dates are then displayed.

# Instance considerations

Instances that should be autopatched must be listed in the [Managed Instances](https://console.aws.amazon.com/systems-manager/managed-instances) list of AWS Systems Manager. To show up in this list, the instances must meet the [Systems Manager Prerequisites](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-prereqs.html).
