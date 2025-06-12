# AMI
An Amazon Machine Image (AMI) is a pre-configured template that provides the necessary information to launch an EC2
instance in AWS.

It includes:
* Operating system (e.g., Linux, Windows)
* Application server (e.g., Apache, Nginx)
* Pre-installed software and configurations

With an AMI, you can launch new EC2 instances with a consistent, predefined configuration.

You can also create custom AMIs to include specific software or settings, allowing for quick replication of
environments.

## What is an Amazon Machine Image (AMI)?
An AMI is essentially a blueprint or template that contains all the information needed to launch an EC2 instance. Think 
of it as a snapshot of a complete computer system that includes the operating system, applications, configurations, and
even data. When you launch an EC2 instance, you're essentially creating a virtual machine based on this blueprint.

## Key Components of an AMI
- **Root Volume Snapshot**: Contains the operating system and boot information
- **Launch Permissions**: Controls who can use the AMI
- **Block Device Mapping**: Specifies which EBS volumes or instance store volumes to attach
- **Architecture**: x86_64 or ARM64 processor architecture
- **Virtualization Type**: Hardware Virtual Machine (HVM) or Paravirtual (PV)
- **Boot Mode**: Legacy BIOS or UEFI

## AMI Architecture Types
### Hardware Virtual Machine (HVM)
- **Current Standard**: All modern instance types support HVM
- **Performance**: Better performance and access to enhanced networking
- **Features**: Supports SR-IOV, enhanced networking, GPU instances
- **Compatibility**: Works with all current EC2 instance types

### Paravirtual (PV)
- **Legacy**: Older virtualization technology
- **Limitations**: Cannot use enhanced networking or GPU instances
- **Use Cases**: Some specialized legacy applications
- **Recommendation**: Use HVM for new deployments

## Types of AWS AMIs

* **Public AMIs:** Available to all AWS users. Useful for basic use cases like popular operating systems (e.g., Ubuntu,
  CentOS).
* **Private AMIs:** Created by a user and only available within that account or shared with specific accounts.
* **Paid AMIs/Marketplace AMIs:** Provided by third parties through AWS Marketplace, offering software like databases,
  web servers, or pre-configured environments.

### Public AMIs
- **AWS-Provided**: Amazon Linux, Ubuntu, Windows Server, RHEL
- **Community**: Shared by other AWS users
- **Free**: No additional software licensing costs
- **Security**: Regularly updated by providers
- **Examples**: Amazon Linux 2, Ubuntu 20.04 LTS, Windows Server 2019

### Private AMIs
- **Custom Images**: Created from your own instances
- **Account Specific**: Only visible within your AWS account
- **Sharing**: Can be shared with specific AWS accounts
- **Use Cases**: Standardized deployments, golden images
- **Control**: Full control over image content and updates

### AWS Marketplace AMIs
- **Third-Party Software**: Pre-configured solutions from vendors
- **Licensing**: Often includes software licensing costs
- **Categories**: Databases, security tools, development environments
- **Support**: Vendor-provided support and maintenance
- **Examples**: MongoDB, GitLab, Splunk, various Linux distributions

### Community AMIs
- **User-Generated**: Created and shared by AWS community
- **Free**: No licensing costs (usually)
- **Caution**: Limited support and security validation
- **Use Cases**: Learning, testing, specialized configurations

## Use Case of Paid AMIs

**Benefits:**
* **Rapid Deployment:** The LAMP Stack is pre-configured, eliminating the need to manually install and configure Apache,
  MySQL, and PHP.
* **Scalability and Load Balancing:** Running on AWS enables quick scaling to match website traffic, while Elastic Load
  Balancer helps in distributing requests.
* **Cost Efficiency:** You only pay for the infrastructure and the software according to your usage.

### Additional Benefits of Paid AMIs
* **Professional Support**: Vendor-provided technical support
* **Regular Updates**: Automated security patches and software updates
* **Compliance**: Pre-configured for various compliance standards
* **Documentation**: Comprehensive setup and configuration guides
* **Integration**: Pre-configured integrations with other AWS services

### Popular Marketplace Categories
- **Development Tools**: IDEs, CI/CD platforms, version control systems
- **Database Solutions**: MongoDB, PostgreSQL, MySQL optimized images
- **Security Solutions**: Firewalls, vulnerability scanners, compliance tools
- **Business Applications**: CRM, ERP, collaboration platforms
- **Machine Learning**: Pre-configured ML frameworks and tools

## Creating Custom AMIs

### Creating an image
Suppose we have an EC2 instance running a web server with some dependencies installed.Like Nginx, Node.js, and a web
application. So we can crate image of this instance using EC2 Image Builder. So when traffic increases, we can launch
new instances from this image to handle the load without having to manually configure each instance.

from EC2 > Instances, select the instance, click on "Actions" > Image and templates > Create image. We can give name
like "WebServerImage-v1" and description like "Image of web server with Nginx and Node.js". `-v1` for versioning. Also,
can check "Reboot instance" to ensure a clean state for the image. If we check this it will reboot the instance before
creating the image, ensuring that all changes are saved and the instance is in a consistent state. Also while creating
image we can extra volume if needed.

At EC2 > Images > AMIs, we can see the newly created image. Now we can lunch new instances from this image by selecting
the image and clicking on "Launch instance from AMI". This will take us to the instance launch wizard, where we can
update instance type, security groups, and other settings as needed.

### AMI Creation Process
1. **Prepare Instance**: Install and configure all required software
2. **Clean Up**: Remove sensitive data and temporary files
3. **Stop Instance**: Recommended for data consistency (optional)
4. **Create Image**: Use AWS Console, CLI, or API
5. **Test AMI**: Launch test instance to verify functionality
6. **Tag and Document**: Add metadata and documentation

### AMI Creation Best Practices
- **Stop Instance**: Stop instance before creating AMI for consistency
- **Clean Up**: Remove logs, temporary files, and sensitive data
- **Remove Keys**: Delete SSH keys and AWS credentials
- **Update Software**: Ensure all software is updated
- **Document Changes**: Maintain changelog for versioning
- **Test Thoroughly**: Validate AMI functionality before production use

### Run Cleanup Script
A cleanup script is used to remove sensitive data, user accounts, and configurations from an EC2 instance before
creating an AMI. This ensures that the AMI does not contain any sensitive information or user-specific data, making it
safe to share or use for scaling.

```bash
#!/bin/bash

# Remove SSH keys
rm -rf ~/.ssh/authorized_keys

# Clear user credentials and history
rm -rf ~/.aws/credentials
rm -rf ~/.git-credentials
rm -rf ~/.bash_history

# Clean system logs and temporary files
rm -rf /var/log/*
rm -rf /tmp/*
rm -rf /var/tmp/*

# Remove user accounts
deluser tempuser --remove-home

# Lock root account
passwd -l root

# Reset configuration files (example for Nginx)
rm -rf /etc/nginx/nginx.conf
```

### Enhanced Cleanup Script
```bash
#!/bin/bash

# Comprehensive cleanup script for AMI preparation

# 1. Remove SSH host keys (will be regenerated on first boot)
sudo rm -f /etc/ssh/ssh_host_*

# 2. Remove user-specific files
sudo rm -rf /home/*/.ssh/authorized_keys
sudo rm -rf /home/*/.bash_history
sudo rm -rf /home/*/.viminfo
sudo rm -rf /root/.ssh/authorized_keys
sudo rm -rf /root/.bash_history

# 3. Clear AWS credentials and configuration
sudo rm -rf /home/*/.aws/
sudo rm -rf /root/.aws/

# 4. Clean logs and temporary files
sudo rm -rf /var/log/*.log
sudo rm -rf /var/log/*.gz
sudo rm -rf /tmp/*
sudo rm -rf /var/tmp/*
sudo rm -rf /var/cache/apt/archives/*.deb

# 5. Clear command history
history -c
sudo history -c

# 6. Remove machine-specific files
sudo rm -f /etc/machine-id
sudo rm -f /var/lib/dbus/machine-id

# 7. Clear network configuration (if using DHCP)
sudo rm -f /etc/netplan/50-cloud-init.yaml

# 8. Clear cloud-init data
sudo rm -rf /var/lib/cloud/instances/*
sudo rm -rf /var/lib/cloud/data/*

# 9. Zero out free space (optional - reduces AMI size)
# sudo dd if=/dev/zero of=/zero.fill bs=1M || true
# sudo rm -f /zero.fill

echo "Cleanup completed. Instance is ready for AMI creation."
```

### Creating template from instance
From EC2 > Instance, select the instance, click on "Actions" > Image and templates > Create template from instance. We
can give name like "WebServerTemplate-v1" and description like "Template for web server with Nginx and Node.js". Now we
will get this template at EC2 > Instances > Launch Templates. This template can be used to launch new instances with the
same configuration as the original instance, including the AMI, instance type, security groups, and other settings.

For lunching new instance from this template, we can select the template and click on Action > Launch instance from
template. This will take us to the instance launch wizard.

### Launch Template Benefits
- **Versioning**: Maintain multiple versions of configurations
- **Auto Scaling**: Required for Auto Scaling Groups
- **Consistent Deployments**: Standardize instance configurations
- **Spot Instances**: Simplified spot instance requests
- **Mixed Instance Types**: Support for multiple instance types
- **Parameter Flexibility**: Override specific parameters during launch

## Create Launch Template vs. Create Image (AMI)

| Feature         | Create Launch Template                                                            | Create Image (AMI)                                                 |
|:----------------|:----------------------------------------------------------------------------------|:-------------------------------------------------------------------|
| **Purpose**     | Create a reusable blueprint for launching instances                               | Create a custom AMI snapshot of an instance                        |
| **Content**     | Contains configuration settings (e.g., AMI, instance type, security groups, etc.) | Captures OS, applications, configurations, and data                |
| **Reusability** | Used repeatedly to launch new instances in a consistent way                       | Used to create new instances that replicate the captured image     |
| **Use Cases**   | Auto Scaling, Spot Instances, Standardizing instance settings                     | Backup, Replication, Migrating instances to another region         |
| **Versioning**  | Can have multiple versions for different configurations                           | Typically, an AMI is a point-in-time capture of an instance        |

### Extended Comparison

| Aspect           | Launch Template                  | AMI                                   |
|------------------|----------------------------------|---------------------------------------|
| **Storage**      | Lightweight (just configuration) | Heavy (full disk image)               |
| **Speed**        | Fast to create                   | Slower to create                      |
| **Cost**         | Free                             | Storage costs for snapshots           |
| **Sharing**      | Account/region specific          | Can be shared across accounts/regions |
| **Flexibility**  | High (can override settings)     | Medium (fixed OS/software)            |
| **Auto Scaling** | Required                         | Not directly usable                   |
| **Backup**       | Configuration only               | Complete system backup                |

## AMI Lifecycle Management

### AMI States
- **Pending**: AMI creation in progress
- **Available**: AMI is ready for use
- **Invalid**: AMI creation failed
- **Deregistered**: AMI has been removed but snapshots may remain

### AMI Operations
- **Copy**: Copy AMI to different regions
- **Modify**: Change permissions and attributes
- **Deregister**: Remove AMI (snapshots remain)
- **Share**: Grant access to other AWS accounts
- **Make Public**: Allow all AWS users to access

### Version Management
```bash
# Naming convention examples
WebApp-v1.0.0-20240101
Database-MySQL-v2.1-production
DevEnvironment-NodeJS-v1.2-staging

# Tag examples
Environment: Production
Version: 1.0.0
Application: WebServer
Created-By: DevOps-Team
Backup-Schedule: Daily
```

## Summary

* Yes, all installed applications, configuration settings, environment variables, network configurations, DNS settings,
  users, and firewall settings will be included in the AMI.
* An AMI is essentially a **complete snapshot** of the instance at the point in time you created the image, allowing you
  to replicate the exact state of that server, including all software, configuration, and operating system-level changes.
* When you create an EC2 instance from this AMI, it will boot up as if it were an exact clone of the original, with all
  installed software and settings intact.

### What's Included in an AMI
- **Operating System**: Complete OS installation and configuration
- **Installed Software**: All applications, packages, and dependencies
- **System Configuration**: Network settings, users, permissions
- **Application Data**: Databases, logs, application files (at creation time)
- **Environment Variables**: System and user-defined variables
- **Startup Scripts**: Services and automated startup processes
- **Security Settings**: Firewall rules, SELinux policies, etc.

### What's NOT Included
- **Instance Store Data**: Temporary storage data is not preserved
- **Dynamic Network Configuration**: Instance-specific IP addresses
- **SSH Host Keys**: Regenerated on instance launch
- **Instance Metadata**: Instance ID, placement information
- **EBS Volumes**: Additional EBS volumes (unless explicitly included)

## EC2 Image Builder
* Automate VM or Image Creation
  * creation, testing, and deployment of AMIs
* Can be configured to run at regular intervals (e.g., daily, weekly, or monthly)
* Free

### EC2 Image Builder Features
- **Automated Pipelines**: Scheduled image builds
- **Component-based**: Reusable building blocks
- **Testing**: Built-in and custom testing capabilities
- **Multi-Region**: Distribute images across regions
- **Compliance**: Security hardening and compliance validation
- **Integration**: Works with Systems Manager, CloudFormation

### EC2 Image Builder Workflow

1.  **Base Image:** The process starts with a base image.
2.  **EC2 Image Builder:** The base image is used to start a build process in EC2 Image Builder.
3.  **Customize:** Customize the software installed on the image.
4.  **Secure:** Secure the image using AWS-provided and/or custom security templates.
5.  **Test:** Test the image with AWS-provided tests and/or your own custom tests.
6.  **Distribute:** Distribute the final "golden" image to selected AWS regions.

### Detailed Image Builder Components

#### Image Recipe
- **Base Image**: Starting point (AMI or container image)
- **Components**: Software and configurations to install
- **Working Directory**: Temporary build environment
- **Block Device Mappings**: Storage configuration

#### Infrastructure Configuration
- **Instance Type**: Build instance specifications
- **Instance Profile**: IAM role for build process
- **Security Groups**: Network access for build instance
- **VPC Settings**: Network configuration
- **SNS Topics**: Notifications for build events

#### Distribution Settings
- **Target Regions**: Where to distribute the image
- **AMI Tags**: Metadata for created images
- **License Configuration**: License tracking
- **Account Sharing**: Cross-account distribution

### Image Builder Components
```yaml
# Example component for installing Docker
name: InstallDocker
description: Install and configure Docker
schemaVersion: 1.0

phases:
  - name: build
    steps:
      - name: UpdateOS
        action: UpdateOS
      - name: InstallDocker
        action: ExecuteBash
        inputs:
          commands:
            - curl -fsSL https://get.docker.com -o get-docker.sh
            - sh get-docker.sh
            - usermod -aG docker ec2-user
            - systemctl enable docker
            - systemctl start docker
```

## AMI Security and Compliance

### Security Best Practices
- **Minimize Software**: Install only necessary packages
- **Regular Updates**: Keep OS and software updated
- **Remove Secrets**: No hardcoded passwords or keys
- **Audit Tools**: Include security monitoring tools
- **Encryption**: Enable EBS encryption for sensitive data
- **Access Control**: Limit AMI sharing and permissions

### Compliance Considerations
- **CIS Benchmarks**: Center for Internet Security hardening
- **STIG Compliance**: Security Technical Implementation Guides
- **PCI DSS**: Payment Card Industry compliance
- **HIPAA**: Healthcare data protection requirements
- **SOC 2**: Service Organization Control audits

### Security Scanning
```bash
# Example security scanning tools to include
# Install ClamAV antivirus
sudo yum install -y clamav clamd
sudo freshclam

# Install and configure fail2ban
sudo yum install -y fail2ban
sudo systemctl enable fail2ban

# Install security updates
sudo yum update -y --security

# Configure automatic security updates
echo "0 2 * * * root /usr/bin/yum update -y --security" >> /etc/crontab
```

## AMI Cost Optimization

### Cost Components
- **EBS Snapshots**: Storage costs for AMI backing snapshots
- **Data Transfer**: Cross-region copying costs
- **Build Instances**: EC2 costs during Image Builder processes
- **Storage Duration**: Ongoing costs for unused AMIs

### Cost Optimization Strategies
- **Regular Cleanup**: Deregister unused AMIs
- **Snapshot Management**: Delete orphaned snapshots
- **Compression**: Use efficient file systems and cleanup
- **Regional Strategy**: Store AMIs only where needed
- **Lifecycle Policies**: Automate AMI lifecycle management

### Cost Monitoring
```bash
# List AMIs and their snapshots
aws ec2 describe-images --owners self --query 'Images[*].[ImageId,Name,CreationDate]'

# Find snapshots associated with AMIs
aws ec2 describe-snapshots --owner-ids self --query 'Snapshots[*].[SnapshotId,Description,StartTime,VolumeSize]'

# Calculate AMI storage costs
aws ec2 describe-snapshots --owner-ids self --query 'sum(Snapshots[*].VolumeSize)'
```

## AMI Troubleshooting

### Common Issues
- **Launch Failures**: Invalid AMI or missing components
- **Performance Issues**: Incorrectly sized instances
- **Network Problems**: Security group or VPC misconfigurations
- **Software Errors**: Missing dependencies or configurations
- **Permission Errors**: Insufficient IAM permissions

### Debugging Steps
1. **Check AMI Status**: Verify AMI is available
2. **Validate Configuration**: Review instance type compatibility
3. **Test Launch**: Try launching in different subnets/AZs
4. **Review Logs**: Check EC2 console and CloudTrail logs
5. **Instance Logs**: Examine /var/log/cloud-init-output.log

### Troubleshooting Commands
```bash
# Check AMI details
aws ec2 describe-images --image-ids ami-12345678

# Validate launch template
aws ec2 describe-launch-templates --template-names MyTemplate

# Check instance status
aws ec2 describe-instance-status --instance-ids i-12345678

# Review cloud-init logs
sudo cat /var/log/cloud-init-output.log
sudo cat /var/log/cloud-init.log

# Check system status
sudo systemctl status
journalctl -xe
```

## AMI Migration and Portability

### Cross-Region Migration
1. **Copy AMI**: Use copy-image API or console
2. **Update References**: Modify templates and configurations
3. **Test Functionality**: Validate in target region
4. **Update Documentation**: Record new AMI IDs

### Cross-Account Sharing
```bash
# Share AMI with another account
aws ec2 modify-image-attribute --image-id ami-12345678 --launch-permission "Add=[{UserId=123456789012}]"

# Make AMI public (use with caution)
aws ec2 modify-image-attribute --image-id ami-12345678 --launch-permission "Add=[{Group=all}]"

# Copy shared AMI to your account
aws ec2 copy-image --source-region us-east-1 --source-image-id ami-12345678 --name "CopiedAMI"
```

### Hybrid Cloud Considerations
- **VM Import/Export**: Migrate VMs from on-premises
- **Format Support**: VMDK, VHD, OVA formats
- **Licensing**: Consider licensing implications
- **Network Configuration**: Plan for network differences

## Advanced AMI Patterns

### Golden Image Strategy
- **Base Image**: Minimal, hardened OS installation
- **Application Layers**: Separate images for different applications
- **Configuration Management**: Use user data for environment-specific config
- **Immutable Infrastructure**: Replace rather than update instances

### Multi-Stage Images
```bash
# Stage 1: Base OS hardening
BaseOS-Hardened-v1.0

# Stage 2: Runtime environment
BaseOS-Hardened-Java11-v1.0

# Stage 3: Application specific
WebApp-Production-v2.1
```

### Blue-Green Deployments
- **Current Version**: Blue AMI running production
- **New Version**: Green AMI for testing
- **Switch Over**: Update launch templates
- **Rollback Plan**: Keep previous AMI for quick rollback

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)