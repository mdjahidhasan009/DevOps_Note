## EBS vs EFS Comparison

### EBS – Elastic Block Storage

-   **Connectivity:** Bound to a single EC2 instance at a time (except for `io1`/`io2` Multi-Attach).
-   **Availability Zone (AZ) Scope:** EBS volumes are locked at the Availability Zone (AZ) level.
-   **Performance Scaling:**
    -   **gp2:** IOPS performance increases as the disk size increases.
    -   **gp3 & io1:** You can increase IOPS independently of the volume size.
-   **Migrating an EBS volume across AZs:**
    -   Take a snapshot of the volume.
    -   Restore the snapshot to another AZ.
    -   **Note:** EBS backups use I/O, so you should avoid running them while your application is handling high traffic.
-   **Data Persistence:**
    -   Root EBS volumes of instances are **terminated by default** if the EC2 instance is terminated. (This behavior can be disabled).

### EFS – Elastic File System

-   **Connectivity:** Can be mounted on hundreds of instances across multiple Availability Zones.
-   **Common Use Case:** Sharing website files (e.g., for WordPress).
-   **Compatibility:** Only for **Linux Instances** (as it is a POSIX-compliant file system).
-   **Cost:**
    -   EFS has a higher price point than EBS.
    -   Can leverage **Storage Tiers** (e.g., Standard-IA) for cost savings.
-   **Key Takeaway:** Remember the distinct use cases for **EFS vs EBS vs Instance Store**.


# Resources
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)