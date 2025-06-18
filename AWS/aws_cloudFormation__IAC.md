# AWS CloudFormation

AWS CloudFormation is an Infrastructure as Code (IaC) service that lets you define, provision, and manage AWS resources
in a declarative, template-based format.

## Key Benefits

*   **"Consistency:** Define infrastructure in code"
*   **"Automation:** Reduce manual work"
*   **"Repeatable:** Replicate environments easily"

## Key Features

* **Infrastructure as Code (IaC) Tool:** Automates the creation, management, and updating of AWS infrastructure.
* **Declarative Language:** Users define desired end states for resources, and CloudFormation handles provisioning.
* **Template-Based:** Uses YAML or JSON templates to specify AWS resources and configurations.
* **AWS Native:** Exclusively supports AWS resources, fully integrated with AWS services.
* **State Management:** Manages state internally, eliminating the need for separate state files.
* **Stacks and Stack Sets:** Organizes resources in stacks for easier management and allows for multi-account and 
  region deployments with stack sets.
* **Cost-Free Tool:** CloudFormation itself is free; you only pay for the AWS resources created.
* **Drift Detection:** Identifies and reports on resource changes made outside of CloudFormation to ensure resources 
  stay aligned with the template.

## Example Template (YAML)

```yaml
Resources:
  SimpleEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      InstanceType: t3.micro
      ImageId: ami-02db68a01488594c5 # AMI ID for your region
      Tags:
        - Key: Name
          Value: MySimpleInstance
```

## Use cases:

* Create EC2 Instances with Security Groups and Elastic IPs
* Provision S3 Buckets with HTTP Endpoints
* Set Up VPCs with Subnets and Route Tables
* Create RDS Databases with Automatic Backups
* Deploy Lambda Functions with API Gateway Integrations
* Set Up Elastic Load Balancers and Auto Scaling for Web Applications
* Deploy IAM Roles, Policies, and User Access Management





# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)