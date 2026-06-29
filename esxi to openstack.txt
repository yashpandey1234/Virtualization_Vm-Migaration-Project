# **file:///C:/Users/yash.pandey/Downloads/esxi\_to\_openstack\_architecture.html**

# 













































4\. OpenStack Side Components

Keystone



Authentication



Nova



VM create karta hai.



Glance



Images store karta hai.



Neutron



Network mapping.



Cinder



Volumes/storage.



vJailbreak OpenStack SDK use karke VM, volume aur network create karta hai.



6\. CBT Kya Hai? (VERY IMPORTANT)

CBT



Change Block Tracking



VMware feature.



Ye track karta hai ki VM disk ke kaunse blocks change hue hain.



Example:



Day 1



100 GB VM



Day 2







# **VMware**

# **↓**

# **vCenter**

# **↓**

# **vJailbreak**

# 

# **↓**

# **VDDK**

# **↓**

# **Disk Read**

# 

# **↓**

# **CBT**

# **↓**

# **Only Changed Data**

# 

# **↓**

# **virt-v2v**

# **↓**

# **VMDK → QCOW2**

# 

# **↓**

# **OpenStack**

# 

# **↓**

# **Nova**

# **Create VM**

# 

# **↓**

# **Neutron**

# **Attach Network**

# 

# **↓**

# **Cinder**

# **Attach Storage**

# 

# **↓**

# **VM Running**











**Theek. Ab vJailbreak ke actual important components aur OpenStack ke important components ko proper documentation format me de raha hoon:**



**Format:**



**✅ Formal Definition (4-5 lines)**

**✅ Simple Definition (2 lines)**

**✅ Example**

**✅ vJailbreak/MigrateAI me Use**



**VJAILBREAK COMPONENTS**

**1. Migration Controller**

**Formal Definition**



**Migration Controller is the central orchestration component of vJailbreak responsible for managing the entire VM migration lifecycle. It continuously monitors migration requests, creates migration tasks, schedules workers, tracks progress, handles failures, and updates migration status. The controller acts as the brain of the platform and coordinates communication between VMware, migration workers, and OpenStack services.**



**Simple Definition**



**Migration Controller vJailbreak ka brain hai.**



**Ye decide karta hai kis VM ki migration kab aur kaise hogi.**



**Example**

**User:**

**Migrate 20 VMs**



**Controller:**

**Create 20 migration jobs**

**Assign workers**

**Track status**

**Project Use**



**MigrateAI me ye role LangGraph + FastAPI perform karega.**



**2. govmomi**

**Formal Definition**



**govmomi is VMware's Go SDK that provides programmatic access to vSphere APIs. It enables applications to interact with vCenter, ESXi hosts, virtual machines, snapshots, networks, and datastores. govmomi is widely used in VMware automation and migration tools.**



**Simple Definition**



**Ye Go library hai jo VMware se baat karti hai.**



**vJailbreak isi se VMware inventory fetch karta hai.**



**Example**

**vm.Name**

**vm.MemoryMB**

**vm.NumCPU**

**Project Use**



**MigrateAI me govmomi ki jagah pyVmomi use hoga.**



**3. VDDK**

**Formal Definition**



**VMware Virtual Disk Development Kit (VDDK) is VMware's official SDK used for accessing and reading virtual machine disk files. It provides APIs that allow backup and migration solutions to interact directly with VMDK files while maintaining VMware compatibility and performance.**



**Simple Definition**



**VMware disk padhne ki official key.**



**Bina VDDK VMware disk access nahi kar sakte.**



**Example**

**WindowsServer.vmdk**

        \*\*↓\*\*

&nbsp;     \\\*\\\*VDDK\\\*\\\*

        \\\*\\\*↓\\\*\\\*





**Read Disk Blocks**

**Project Use**



**Migration Agent VMware disks read karega.**



**4. CBT (Change Block Tracking)**

**Formal Definition**



**CBT is a VMware feature that records which disk blocks have changed since the last synchronization or backup operation. It enables incremental copying of data instead of transferring the entire disk repeatedly.**



**Simple Definition**



**Sirf changed data track karta hai.**



**Migration ko fast banata hai.**



**Example**

**VM Disk = 500 GB**



**Changed = 2 GB**



**Copy only 2 GB**

**Project Use**



**Hot migration ke liye.**



**5. nbdkit**

**Formal Definition**



**nbdkit is a flexible Network Block Device server that allows storage devices and virtual disks to be exposed over a network. It is commonly used to stream virtual disk data during migration processes.**



**Simple Definition**



**Disk ko network ke through stream karta hai.**



**Example**

**VMware Disk**

     \*\*↓\*\*



**nbdkit**

     \*\*↓\*\*



**Migration Host**

**Project Use**



**VDDK se disk data transfer.**



**6. virt-v2v**

**Formal Definition**



**virt-v2v is an open-source virtual machine conversion tool used to migrate VMs between hypervisors. It converts VMware VMDK disks into KVM/OpenStack compatible QCOW2 images while handling drivers and configuration changes.**



**Simple Definition**



**VM conversion machine.**



**Example**

**server.vmdk**

      \*\*↓\*\*



**virt-v2v**

      \*\*↓\*\*



**server.qcow2**

**Project Use**



**Core migration engine.**



**7. Migration Worker**

**Formal Definition**



**Migration Workers are execution units responsible for performing actual migration tasks. Each worker handles disk transfer, conversion, image upload, VM creation, and validation for assigned virtual machines.**



**Simple Definition**



**Actual migration karne wale workers.**



**Example**

**Worker1 → VM1**



**Worker2 → VM2**



**Worker3 → VM3**

**Project Use**



**Celery workers same role perform karenge.**



**8. OpenStack SDK**

**Formal Definition**



**The OpenStack SDK is a client library that enables applications to interact with OpenStack services such as Nova, Glance, Neutron, and Cinder using APIs.**



**Simple Definition**



**OpenStack se baat karne wali library.**



**Example**

**create\_server()**

**create\_volume()**

**Project Use**



**Target VM create karna.**



**OPENSTACK COMPONENTS**

**1. Keystone**

**Formal Definition**



**Keystone is OpenStack's identity and access management service. It provides authentication, authorization, token generation, and service catalog management for all OpenStack services.**



**Simple Definition**



**OpenStack ka login system.**



**Example**

**Username**

**Password**

    \*\*↓\*\*



**Token**

**Project Use**



**MigrateAI OpenStack login karega.**



**2. Nova**

**Formal Definition**



**Nova is OpenStack's compute service responsible for provisioning, scheduling, and managing virtual machine instances. It interacts with hypervisors to create and control VM lifecycles.**



**Simple Definition**



**VM Factory.**



**Example**

**2 CPU**

**4 GB RAM**

**50 GB Disk**



**↓**



**Nova**



**VM create.**



**Project Use**



**Migrated VM create karega.**



**3. Glance**

**Formal Definition**



**Glance is OpenStack's image management service that stores and distributes VM images used for instance creation.**



**Simple Definition**



**Image Store.**



**Example**

**ubuntu.qcow2**



**windows.qcow2**

**Project Use**



**Converted QCOW2 image upload hogi.**



**4. Neutron**

**Formal Definition**



**Neutron is OpenStack's networking service responsible for managing virtual networks, routers, subnets, ports, security groups, and IP allocation.**



**Simple Definition**



**Network Manager.**



**Example**

**Network**

**Subnet**

**Router**

**IP**

**Project Use**



**Migrated VM ko network attach karega.**



**5. Cinder**

**Formal Definition**



**Cinder is OpenStack's block storage service that provides persistent storage volumes which can be attached to virtual machines.**



**Simple Definition**



**External Hard Disk Provider.**



**Example**

**100 GB Volume**



**500 GB Volume**

**Project Use**



**VM disk storage.**



**6. Horizon**

**Formal Definition**



**Horizon is OpenStack's web-based dashboard that allows administrators and users to manage cloud resources through a graphical interface.**



**Simple Definition**



**OpenStack UI.**



**Example**

**Create VM**

**Delete VM**

**Create Network**



**Browser se.**



**Project Use**



**Migration ke baad VM verify kar sakte ho.**



**7. Placement**

**Formal Definition**



**Placement is an OpenStack service that tracks available infrastructure resources such as CPU, RAM, and storage and assists Nova in scheduling instances.**



**Simple Definition**



**Resource scheduler.**



**Example**

**Host1 Full**



**Host2 Free**



**Nova Host2 choose karega.**



**Project Use**



**Migrated VM ko correct host par place karega.**



**8. KVM**

**Formal Definition**



**Kernel-based Virtual Machine (KVM) is the hypervisor most commonly used by OpenStack. It converts Linux into a virtualization platform capable of running multiple virtual machines.**



**Simple Definition**



**Linux ka ESXi.**



**Example**

**Linux**

**↓**

**KVM**

**↓**

**VMs**

**Project Use**



**Final migrated VM KVM par run karegi.**



**End-to-End vJailbreak Flow**

**vCenter**

**↓**

**govmomi**

**↓**

**Migration Controller**

**↓**

**Snapshot**

**↓**

**VDDK**

**↓**

**CBT**

**↓**

**nbdkit**

**↓**

**virt-v2v**

**↓**

**QCOW2**

**↓**

**OpenStack SDK**

**↓**

**Keystone**

**↓**

**Glance**

**↓**

**Nova**

**↓**

**Neutron**

**↓**

**Cinder**

**↓**

**KVM**

**↓**

**Migrated VM Running**



**Yehi 15-20 components presentation me sabse important hain, aur VMware → OpenStack migration ka almost pura technical flow cover kar dete hain.**

# 







OpenStack

 ├─ Keystone

 ├─ Nova

 ├─ Neutron

 ├─ Glance

 ├─ MariaDB

 ├─ RabbitMQ

 ├─ Horizon

 └─ Docker Containers







Kolla

Ansible

Docker

Container

Playbook

Inventory

Bootstrap

Deploy

Nova

Neutron

Glance

Keystone

Horizon

RabbitMQ

MariaDB

HAProxy





















































# 

