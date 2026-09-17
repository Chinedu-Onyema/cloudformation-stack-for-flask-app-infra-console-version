# AWS CloudFormation Deployment Guide: Flask App Infrastructure

This is a complete, step-by-step walkthrough for provisioning the core AWS infrastructure for a Flask-based Employee Directory application using the AWS Management Console and CloudFormation templates.

### PDF GUIDE: [CREATE A CLOUDFORMATION STACK FOR YOUR FLASK APP WITH THE CONSOLE.pdf](https://github.com/user-attachments/files/32284416/CREATE.A.CLOUDFORMATION.STACK.FOR.YOUR.FLASK.APP.WITH.THE.CONSOLE.pdf)

### WATCH VIDEO WALKTHROUGH HERE: https://youtu.be/s5qtAOd_GDc


## PREREQUISITES
An active AWS Account with permissions to manage CloudFormation, EC2, VPCs, IAM, and RDS resources.

Template File: Use the file application_infrastructure_deployment_mgt.yml in this repo

Access to AWS CloudShell or the AWS CLI to query region-specific AMI IDs.


## STEP-BY-STEP PROVISONING GUIDE

### Step 1: Template Upload

Log into the AWS Management Console and search for CloudFormation.

On the CloudFormation Dashboard, click Create Stack (Choose With new resources (standard)).

Under Prerequisite - Prepare template, select Choose an existing template.

Note: Optionally, you can explore the IaC Generator tool to draft templates from pre-existing AWS resources.

Under Template source, select Upload a template file.

Upload 6)_application_infrastructure_deployment_mgt.yml and click Next.

Note: Uploading a template automatically stores it in an AWS-managed CloudFormation S3 bucket, generating an S3 object URL for execution.



### Step 2: Retrieve Regional AMI ID

Before entering parameters, retrieve the latest Amazon Linux 2023 AMI ID for your target AWS region using AWS CloudShell.

Run the following command in CloudShell (update --region if not using eu-north-1):

<PRE>aws ec2 describe-images --owners amazon --filters "Name=name,Values=al2023-ami-2023*" "Name=architecture,Values=x86_64" "Name=virtualization-type,Values=hvm" --query 'Images | sort_by(@, &CreationDate) | [-1].[ImageId,Name,CreationDate]' --output table --region eu-north-1</PRE>

Copy the returned ImageId (e.g., ami-xxxxxxxxxxxxxxxxx) from the output table.



### Step 3: Specify Stack Details & Parameters

Stack Name: Set your preferred identifier (e.g., employee-application-infrastructure).

Parameters Configuration:

AmiId: Replace the default string with the AMI ID retrieved in Step 2.

Database Password: Enter your secure password string.

Database Subnet IDs: Select two private subnets across different Availability Zones.

EC2 Subnet ID: Select one public subnet.

VpcId: Select your active target VPC.

Review parameter mappings against your template specification and click Next.



### Step 4: Configure Stack Options & Failure Behaviors

Under Stack failure options, configure how CloudFormation handles provisioning errors:

Roll back all stack resources (Default): Reverts and deletes all created resources if any single resource fails to deploy. Ensures a clean state without lingering orphaned infrastructure.

Preserve successfully provisioned resources: Retains healthy components (e.g., VPC, Subnets) and only rolls back the specific component that failed (e.g., Database). Ideal for rapid troubleshooting and debugging.



### Step 5: Review, Capabilities & Deployment

Scroll to the Capabilities section at the bottom of the page.

Select the Acknowledgement checkbox to explicitly grant CloudFormation permission to create custom IAM resources and roles.

Review all configured parameters on the Review and create page and click Submit.



### Step 6: Monitor Deployment & Verification

Navigate through the stack management tabs to track execution state:

Stack Info: Displays overall deployment status (CREATE_IN_PROGRESS, CREATE_COMPLETE, or ROLLBACK_COMPLETE).

Events: Live graph showing real-time creation order. Green indicators signify successful resource provisioning.

Resources: Lists every provisioned AWS resource alongside its physical resource ID.

Outputs: Displays key deployment exports (e.g., Database endpoints, Public IPv4 Address, Web Application URLs).

Testing Application Availability:

Select the Outputs tab once status reaches CREATE_COMPLETE.

Copy the exported IPv4 Address or direct application URL.

Open a new browser tab and paste the address to access the live Flask Employee Directory application.



## Iterative Updates (Updating Existing Stack)

To deploy new resources or modify configurations on a running stack:

Update your local application_infrastructure_deployment_mgt.yml definition file.

In the AWS CloudFormation Console, select your active stack (employee-application-infrastructure).

Click Update.

Choose Replace current template, upload the modified .yml file, and follow the prompts to execute a change set preview before applying updates.
