# Hypervisor
Hypervisor is a software that creates and manages virtual machines. It allows multiple operating systems to run
concurrently on a host machine by abstracting the hardware resources.

**Examples:** Oracle VirtualBox, VMware, Microsoft Hyper-V, KVM, Xen.

## What is a Hypervisor?
A hypervisor, also known as a Virtual Machine Monitor (VMM), is system software that creates and runs virtual machines 
by sitting between the hardware and the virtual machines. It manages the distribution of hardware resources among
multiple virtual machines and ensures isolation between them.

### Types of Hypervisors

#### Type 1 Hypervisor (Bare-metal)
Runs directly on the host's hardware. Like VMware vSphere/ESXi, Microsoft Hyper-V (core), Xen, KVM. There is no host
operating system in between the hypervisor and the hardware. It provides better performance and is often used in
data centers and enterprise environments.

**Key Characteristics:**
- Direct hardware access
- Better performance and resource utilization
- Lower latency
- More secure (smaller attack surface)
- Enterprise and production environments

**Popular Type 1 Hypervisors:**
- VMware ESXi/vSphere
- Microsoft Hyper-V Server
- Citrix XenServer
- KVM (Kernel-based Virtual Machine)
- Red Hat Enterprise Virtualization (RHEV)

#### Type 2 Hypervisor (Hosted)
Runs on top of a host operating system. They are separate from the host OS and can run multiple guest operating systems.
If any guest OS crashes, it does not affect the host OS or the other guest OS.

**Key Characteristics:**
- Easier to install and configure
- Good for development and testing
- Better hardware compatibility (uses host OS drivers)
- Overhead due to host OS layer
- Desktop and development usage

**Popular Type 2 Hypervisors:**
- Oracle VirtualBox
- VMware Workstation/Fusion
- Parallels Desktop
- QEMU
- Microsoft Virtual PC (deprecated)

#### Type 0 Hypervisor
Runs on the hardware without a host operating system, often used in embedded systems.

**Key Characteristics:**
- Firmware-level virtualization
- Used in specialized/embedded systems
- Very low overhead
- Limited flexibility

| Type    | Runs On                | Examples                        | Notes                               |
|---------|------------------------|---------------------------------|-------------------------------------|
| Type 1  | Bare-metal hardware    | ESXi, Hyper-V (core), Xen, KVM  | Enterprise-grade performance        |
| Type 2  | Host OS                | VirtualBox, VMware Workstation  | Desktop/development usage           |
| Type 0  | Firmware/Embedded HW   | PowerVM, z/VM, Wind River       | Informal term, used in niche areas  |
| Hybrid  | Minimal host/firmware  | macOS Hypervisor, ACRN          | Mixes traits of Type 1 and Type 2   |

### How Hypervisor Works
* Virtual machines share hardware resources from the host operating system.
* Separate set of virtual hardware is created for each virtual machine like CPU, memory, storage, and network
  interfaces.
* VMs are fully isolated from each other, allowing them to run different operating systems and applications
  simultaneously.
* **Resource Allocation:** Hypervisor manages and allocates physical resources (CPU, RAM, storage) to VMs
* **Hardware Abstraction:** Presents virtualized hardware to guest operating systems
* **Isolation:** Ensures VMs cannot interfere with each other
* **Scheduling:** Manages CPU time-sharing between multiple VMs

### Hypervisor Architecture Diagram
```
            Other Operating Systems for Virtualization
                ↑
            Virtualization -> Hypervisor (Software that creates and manages virtual machines)
                ↑
            Windows -> Host Operating System 
                ↑
            Main Hardware 
```

### Key Functions of Hypervisors
- **Resource Management:** Efficiently allocate CPU, memory, storage, and network resources
- **VM Lifecycle Management:** Create, start, stop, pause, and delete virtual machines
- **Security and Isolation:** Maintain separation between VMs and protect against VM escape attacks
- **Performance Monitoring:** Track resource usage and performance metrics
- **Live Migration:** Move running VMs between physical hosts without downtime
- **Snapshot Management:** Create point-in-time copies of VM state for backup and recovery

# Virtualization
Virtualization is the process of creating virtual versions of physical resources, such as servers, storage devices,
network devices, and even entire operating systems. It allows multiple virtual instances to run on a single physical
machine, enabling better resource utilization, flexibility, and scalability.

It can be done by virtual machines (VMs), containers, or other virtualization technologies.

## Types of Virtualization

### Server Virtualization
- Most common type of virtualization
- Multiple virtual servers run on single physical server
- Each VM has its own OS and applications

### Desktop Virtualization
- Virtual desktop infrastructure (VDI)
- Centralized desktop management
- Remote access to desktop environments

### Application Virtualization
- Applications run in isolated environments
- No installation required on local machine
- Examples: VMware ThinApp, Microsoft App-V

### Storage Virtualization
- Abstract physical storage from logical storage
- Storage pooling and management
- Examples: SAN, NAS virtualization

### Network Virtualization
- Create virtual networks independent of physical hardware
- Software-defined networking (SDN)
- Examples: VLANs, VPNs, overlay networks

## Virtualization Architecture Components

### Virtual Machine (VM)
- Complete virtualized computer system
- Has its own virtual CPU, memory, storage, and network interfaces
- Runs guest operating system and applications
- Isolated from other VMs and host system

### Guest Operating System
- Operating system running inside a virtual machine
- Can be different from host OS
- Believes it's running on physical hardware

### Host Operating System
- Operating system running on physical hardware (Type 2 hypervisors)
- Provides services to hypervisor
- Not present in Type 1 hypervisors

### Virtual Hardware
- **Virtual CPU (vCPU):** Abstracted processor cores allocated to VMs
- **Virtual Memory (vRAM):** Memory allocated from physical RAM pool
- **Virtual Storage:** Virtual disks stored as files on physical storage
- **Virtual Network Interface Cards (vNICs):** Network connectivity for VMs

## Virtualization Use Cases

### Cost Benefits
* **Cheap:** Reduces hardware costs by running multiple VMs on single physical server
* **Server Consolidation:** Multiple physical servers can be consolidated into fewer physical machines
* **Licensing:** Can reduce software licensing costs

### Operational Benefits
* **Reduces:**
  * **Workload:** Easier management of multiple systems
  * **Space:** Less physical footprint in data centers
  * **Energy:** Lower power consumption and cooling costs
* **Easy backup using snapshots:** Point-in-time copies of VM state
* **Easy recovery:** Quick restoration from snapshots or backups
* **Hardware Independence:** VMs can run on different physical hardware

### Development and Testing
* **Multiple OS Testing:** Test applications across different operating systems
* **Development Environments:** Isolated development environments
* **Legacy Application Support:** Run older applications on newer hardware
* **Rapid Provisioning:** Quickly create new test environments
* **Template-based Deployment:** Standardized VM configurations

### Business Continuity
* **Disaster Recovery:** Easy replication and failover
* **High Availability:** Live migration of VMs between hosts
* **Scalability:** Quick provisioning of new VMs as needed
* **Load Balancing:** Distribute workloads across multiple VMs
* **Fault Tolerance:** VM redundancy and automatic failover

### Cloud Computing Enablement
* **Infrastructure as a Service (IaaS):** Foundation for cloud services
* **Multi-tenancy:** Isolated environments for multiple customers
* **Elastic Scaling:** Dynamic resource allocation based on demand

## Common Hypervisor Technologies

### VMware Products
- **ESXi/vSphere:** Enterprise Type 1 hypervisor
- **Workstation:** Type 2 hypervisor for desktops
- **Fusion:** Type 2 hypervisor for macOS
- **vCenter Server:** Centralized management platform

### Microsoft Hyper-V
- **Hyper-V Server:** Standalone Type 1 hypervisor
- **Windows Hyper-V:** Type 2 hypervisor integrated with Windows
- **System Center Virtual Machine Manager:** Management tool

### Open Source Solutions
- **KVM (Kernel-based Virtual Machine):** Linux-based Type 1 hypervisor
- **Xen:** Open source Type 1 hypervisor
- **QEMU:** Machine emulator and virtualizer
- **VirtualBox:** Oracle's free Type 2 hypervisor
- **Proxmox VE:** Open-source virtualization platform

### Cloud Hypervisors
- **Amazon EC2:** Uses Xen and Nitro System
- **Google Compute Engine:** Uses KVM
- **Microsoft Azure:** Uses Hyper-V
- **IBM Cloud:** Uses KVM and PowerVM

## Performance Considerations

### Type 1 vs Type 2 Performance
- **Type 1:** Better performance, direct hardware access
- **Type 2:** Additional overhead due to host OS layer

### Resource Allocation
- **CPU:** Virtual CPU cores mapped to physical cores
- **Memory:** RAM allocated to VMs from physical memory
- **Storage:** Virtual disks stored as files on physical storage
- **Network:** Virtual network interfaces bridged to physical network

### Optimization Techniques
- **CPU Features:** Hardware-assisted virtualization (Intel VT-x, AMD-V)
- **Memory Management:** Balloon drivers, memory overcommitment, transparent page sharing
- **I/O Optimization:** Paravirtualized drivers, SR-IOV, IOMMU
- **Storage:** Thin provisioning, storage deduplication, SSD caching

### Performance Monitoring Metrics
- **CPU utilization and ready time**
- **Memory usage and balloon driver activity**
- **Storage IOPS and latency**
- **Network throughput and packet loss**
- **VM density per host**

## Security Considerations

### VM Isolation
- **Hardware-level isolation:** CPU ring protection, memory protection
- **Network isolation:** Virtual firewalls, VLANs, micro-segmentation
- **Storage isolation:** Encrypted virtual disks, access controls

### Security Best Practices
- **Regular hypervisor updates and patches**
- **VM escape attack prevention**
- **Secure VM templates and hardening**
- **Network security policies and monitoring**
- **Backup and disaster recovery planning**

### Compliance and Auditing
- **Regulatory compliance:** PCI-DSS, HIPAA, SOX
- **Audit trails and logging**
- **Configuration management and change control**

# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)