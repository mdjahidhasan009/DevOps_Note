# AWS IAM
AWS Identity and Access Management (IAM) is a web service that helps you securely control access to AWS services and
resources for your users.

It enables you to manage permissions and policies for users, groups, and roles, ensuring that only authorized users can
access specific AWS resources. Its a global service.

## What is AWS IAM?
IAM is a fundamental security service in AWS that acts as the gatekeeper for your AWS resources. It allows you to create 
and manage AWS users and their access to AWS services and resources securely. Think of IAM as the security guard that 
checks IDs and permissions before allowing anyone into your AWS environment.

## Core IAM Components

### Users
- Individual people or entities that interact with AWS
- Each user has unique security credentials
- Can be assigned permissions directly or through groups
- User can be use aws without being at any group, also can be assign at multiple groups
- Best practice: Create individual users rather than sharing credentials

### Groups
- Collections of users with similar access requirements
- Permissions assigned to groups are inherited by all group members
- Examples: Developers, Administrators, QA Team, people's from same organization
- Users can belong to multiple groups
- Group only containers user's not other groups

### Roles
- Set of permissions that can be assumed by users, applications, or AWS services
- Temporary access credentials are provided when a role is assumed
- No permanent credentials associated with roles
- Common use cases: Cross-account access, EC2 instances accessing other AWS services

### Policies
- JSON documents that define permissions
- Specify what actions are allowed or denied on which resources
- Can be attached to users, groups, or roles
- Two types: AWS managed policies and customer managed policies

## Key Features of AWS IAM
* **User Management**: Create and manage AWS users and groups, and assign permissions to allow or deny access to AWS
  resources.
* **Role-Based Access Control**: Define roles with specific permissions that can be assumed by users or services,
  allowing for temporary access to resources.
* **Policies**: Use JSON-based policies to define permissions for users, groups, and roles, specifying what actions are
  allowed or denied on which resources.
* **Multi-Factor Authentication (MFA)**: Enhance security by requiring users to provide additional verification (like a
  code from a mobile device) when accessing AWS resources.
* **Temporary Security Credentials**: Issue temporary security credentials for users or applications that need to access
  AWS resources without long-term credentials.
* **Integration with Other AWS Services**: IAM integrates with many AWS services, allowing you to control access to
  resources like S3 buckets, EC2 instances, and more.
* **Fine-Grained Access Control**: Define permissions at a granular level, allowing you to specify access to individual
  resources or actions within a service, it should be as less permission as possible
* **Audit and Monitoring**: Use AWS CloudTrail to log and monitor IAM actions, providing visibility into who accessed
  what resources and when.
* **Free Service**: IAM is offered at no additional cost
* **Global Service**: IAM operates globally across all AWS regions
* **Root account created by default, shouldn't be used or shared**

## IAM Policy Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-server-side-encryption": "AES256"
        }
      }
    }
  ]
}
```

### Policy Elements
- **Version**: Policy language version (usually "2012-10-17")
- **Statement**: Main policy element containing permissions
- **Effect**: Allow or Deny
- **Action**: List of API actions the policy applies to
- **Resource**: ARN of the resources the actions apply to
- **Condition**: Optional conditions for when the policy applies

## Types of IAM Policies

### AWS Managed Policies
- Created and maintained by AWS
- Cannot be modified by customers
- Examples: ReadOnlyAccess, PowerUserAccess, AdministratorAccess
- Automatically updated by AWS when new services are released

### Customer Managed Policies
- Created and maintained by AWS customers
- Can be modified as needed
- Reusable across multiple users, groups, or roles
- Version control and rollback capabilities

### Inline Policies
- Directly attached to a single user, group, or role
- One-to-one relationship between policy and identity
- Deleted when the associated identity is deleted

## Common IAM Tasks

* **Create Users**: You can create individual user accounts for people who need access to your AWS resources.
* **Assign Permissions**: You can assign specific permissions to users, groups, or roles to control what actions they
  can perform on AWS services.
* **Create Groups**: You can group users together and assign permissions to the group, making management easier for
  multiple users.
* **Configure MFA**: Set up multi-factor authentication for enhanced security
* **Create Service Roles**: Allow AWS services to perform actions on your behalf
* **Set up Cross-Account Access**: Enable users from other AWS accounts to access your resources
* **Implement Password Policies**: Define password complexity and rotation requirements

## AWS IAM Ways of accessing AWS
* **The AWS Management Console** provides a graphical, web-based approach.
* **The AWS CLI** provides a command-line, scripting approach.
* **AWS SDKs and APIs** offer programmatic, code-based access, allowing users to integrate AWS directly into their
  applications.

### Authentication Methods
- **Username and Password**: For console access
- **Access Keys (Access Key ID + Secret Access Key)**: For programmatic access
- **Temporary Security Credentials**: For role-based access
- **Multi-Factor Authentication (MFA)**: Additional security layer

## IAM Security Features

### Multi-Factor Authentication (MFA)
- **Virtual MFA Device**: Smartphone apps like Google Authenticator, Authy
- **Hardware MFA Device**: Physical devices like YubiKey
- **SMS Text Message**: Phone-based verification (not recommended for root account)

### Password Policy
- Minimum password length
- Require specific character types (uppercase, lowercase, numbers, symbols)
- Password expiration and rotation
- Prevent password reuse
- Account lockout policies

### Access Keys Management
- Rotate access keys regularly
- Use temporary credentials when possible
- Monitor access key usage
- Disable unused access keys

## AWS IAM Best Practices
* **Avoid using root account except for account setup, it should not be used or shared**.
* **Add user to a group and assign permission to group**
* **Use password policy or MFA**
* **Use ACCESS KEYS for CLI/SDK**
* **Never share ACCESS KEYS or Password**
* **Audit the permission using IAM credential report**.

### Additional Best Practices
* **Apply Principle of Least Privilege**: Grant only the minimum permissions required
* **Use Roles for Applications**: Don't embed access keys in application code
* **Enable CloudTrail**: Monitor all IAM actions and API calls
* **Regular Access Reviews**: Periodically review and remove unnecessary permissions
* **Use Policy Conditions**: Add conditions to policies for additional security
* **Separate Environments**: Use different AWS accounts for development, testing, and production
* **Monitor Unused Credentials**: Remove credentials that haven't been used recently

## IAM Monitoring and Auditing

### Credential Report
IAM Dashboard > Access Report > Credential Report > Download Credential Report. This report provides a comprehensive
overview of your IAM users, their access keys, passwords, and MFA status, helping you identify any security gaps or
compliance issues.

### Access Analyzer
- Identifies resources shared with external entities
- Helps ensure intended access only
- Validates policies against security best practices
- Generates findings for review and remediation

### CloudTrail Integration
- Logs all IAM API calls
- Tracks who made changes and when
- Provides audit trail for compliance
- Can trigger alerts for suspicious activities

## Common IAM Use Cases

### Development Team Access
- Create developer group with appropriate permissions
- Use roles for application deployment
- Implement staging and production environment separation

### Cross-Account Access
- Create roles for trusted external accounts
- Temporary access for partners or vendors
- Centralized identity management

### Service-to-Service Communication
- EC2 instances accessing S3 buckets
- Lambda functions calling other AWS services
- Automated backup and monitoring systems

### Compliance and Governance
- Implement organization-wide policies
- Service Control Policies (SCPs) with AWS Organizations
- Regular access reviews and certification

## Troubleshooting IAM Issues

### Common Error Messages
- **Access Denied**: Check policy permissions and resource ARNs
- **Invalid User**: Verify user exists and is active
- **Token Expired**: Refresh temporary credentials
- **MFA Required**: Complete multi-factor authentication

### Debugging Tips
- Use IAM Policy Simulator to test permissions
- Check CloudTrail logs for detailed error information
- Verify resource-based policies (like S3 bucket policies)
- Ensure correct region for regional services

## IAM Limits and Quotas
- **Users per account**: 5,000 (can be increased)
- **Groups per account**: 300
- **Roles per account**: 1,000
- **Policies per user/group/role**: 10 managed policies
- **Policy size**: 2,048 characters for inline policies, 6,144 characters for managed policies

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
* [Ultimate AWS Certified Solutions Architect Associate 2025 by Stephane Maarek](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)