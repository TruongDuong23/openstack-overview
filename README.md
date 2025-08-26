# Openstack-overview

**Openstack** là nền tảng điện toán đám mây mã nguồn mở (open-source cloud computing platform) cho phép xây dựng và quản lý các dịch vụ cloud (public cloud and private cloud). Giống như framework của dịch vụ cloud như AWS, GCP, AZURE nhưng mình có thể kiểm soát nó. Nó cung cấp các công cụ để quản lý và cung cấp tài nguyên compute, storage và networking, giúp các tổ chức xây dựng và triển khai hạ tầng đám mây của riêng mình.

OpenStack là nó là một tập hợp nhiều phần mềm chứ không phải là một khối phần mềm đơn khối lớn. Nó bao gồm một số phần độc lập được gọi là các dịch vụ OpenStack. Lý do chính dịch vụ của OpenStack nhằm để đơn giản hoá việc thiết kế và triển khai hạ tầng cloud.


<img width="600" height="251" alt="image" src="https://github.com/user-attachments/assets/3c3cf973-de26-4411-b75b-035db966f42d" />


Administrators và Users có thể truy cập dashboard để khởi tạo máy ảo, cấu hình storage, cấu hình networking hoặc thực hiện bất kỳ thao tác nào mà OpenStack có thể thực hiện thông qua việc có thể gọi APIs.

Có 3 cách để sử dụng Openstack:
1. Horizon dashboard. (Đây là cách đơn giản nhất, linh hoạt và dễ sử dụng)
2. CLI
3. APIs Call

# Component of Openstack
- **Nova**: Quản lý compute resources (VMs and instance)
- **Neutral**: Quản lý networking (virutal network, routers, firewalls, LBs,..)
- **Cinder**: Quản lý block storage (ổ đĩa cứng ảo)
- **Swift**: Quản lý object storage (AWS S3)
- **Keystone**: Quản lý dịch vụ xác thực, ủy quyền truy cập hệ thống (authorization and authentication)
- **Glance**: Quản lý dịch vụ image
- **Horizon**: Web-based dashboard quản lý cloud
- **Ceilometer/Telemetry**: Monitoring and billing
- **Manila**: Quản lý shared file system

# Advantages of Openstack
- **Open-source**: --> tự do sử dụng, sửa đổi và phân phối phần mềm
- **Khả năng mở rộng**: cho phép người dùng dễ dàng tăng hoặc giảm tài nguyên theo yêu cầu
- **Tính linh hoạt**: hỗ trợ nhiều loại phần cứng, phần mềm và tích hợp các hệ thống hiện có
- **Tiết kiệm chi phí**: các tổ chức giảm chi phí đầu tư vào phần cứng, phần mềm, chi phí vận hành và bảo trì hệ thống.
- **Kiểm soat hoàn toàn**: user kiểm soát hoàn toàn cơ sở hạ tầng của mình từ quản lý tài nguyên --> triển khai ứng dụng.

# Openstack Architecture diagram
```sequence
                +-----------------------------+
                |        Horizon (Dashboard)  |
                +-----------------------------+
                           |
                           v
+--------------------------------------------------------------+
|                     Identity (Keystone)                      |
+--------------------------------------------------------------+
   |                 |                 |                |
   v                 v                 v                v
 Compute (Nova)   Networking (Neutron) Storage        Orchestration
   |                 |           +-----+-----+         (Heat)
   |                 |           |           |
   v                 v           v           v
 Hypervisors     Virtual       Block       Object
 (KVM, Xen,     Networks      Storage     Storage
 VMware)        & Routers     (Cinder)    (Swift)

+--------------------------------------------------------------+
| Supporting Services:                                         |
| Glance (Image Service), Ceilometer/Telemetry, Barbican (Key) |
+--------------------------------------------------------------+
```

## Conceptual architecture

<img width="983" height="796" alt="Screenshot 2025-08-20 at 23 57 08" src="https://github.com/user-attachments/assets/d93e6ec6-db0b-4dea-969b-fde9826b16cd" />

## Logical architecture

<img width="986" height="477" alt="Screenshot 2025-08-21 at 00 02 38" src="https://github.com/user-attachments/assets/26fbfd7f-5e42-4709-a9a5-a86d1c6c08f5" />

# Block Storage, File Storage and Object Storage
## Block Storage
- Dữ liệu được lưu trữ trong các khối có kích thước cố định (vd: 2MB, 4GB). Mỗi khối có địa chỉ riêng nhưng không có metadata.
- Hệ điều hành hoặc ứng dụng thường quyết định cách sắp xếp các khối thành tệp. Thường được sử dụng cho csdl, máy ảo,...
- Protocols: fibre channel
- Ưu điểm:
    - Hiệu suất cao, độ trễ thấp
    - Tốt cho dữ liệu có cấu trúc (DBs, VMs)
- Nhược điểm:
    - No metadata (raw blocks) --> gây khó khăn việc quản lý và tìm kiếm dữ liệu
    - Cần hệ thống tập tin (vd: ext4, NTFS, XFS) ở trên để hiểu nó (Không hỗ trợ truy cập cục bộ, cần kết nối internet để truy cập dữ liệu.)
- Giống như ổ cứng trống --> có thể phân vùng (partition) và định dạng (format) mong muốn
## File Storage
- Dữ liệu được lưu trữ dữ liệu dưới dạng tệp được sắp xếp hierarchy of directorie, giúp người dùng dễ dàng truy xuất, quản lý và tìm kiếm thông tin cần thiết.
- Sử dụng các giao thức chia sẻ tệp tiêu chuẩn như NFS, SMB/CIFS. Có thể truy cập như một ổ đĩa dùng chung.
- Protocols: NFS
- Ưu điểm:
    - Dễ dàng sử dụng và chia sẻ (mount và browse như 1 thư mục bình thường)
    - Giàu metadata -> bảo mật và sao lưu dữ liệu mạnh mẽ (permissions, files name,...)
    - Khả năng chia sẻ, quyền truy cập dữ liệu dễ dàng giữa người dùng và thiết bị
    - Chi phí thấp hơn so với triển khai hệ thống lưu trữ riêng
- Nhược điểm:
    - Khó quản lý và truy xuất với số lượng tệp lớn
    - Khó làm việc với dữ liệu phi cấu trúc
    - Chi phí có thể sẽ có phần đắt đỏ với quy mô lớn.
-  Giống như ổ đĩa mạng được chia sẻ hoặc thư mục Google Drive → bạn sẽ thấy các tệp và thư mục có tên và quyền.
  ## Object Storage
- Dữ liệu được lưu trữ dưới dạng đối tượng trong một cấu trúc phẳng. Mỗi đối tượng bao gồm dữ liệu, siêu dữ liệu và một ID duy nhất (thay vì các thư mục phân cấp).
- Truy cập thông qua API (REST, S3 API). Được tối ưu hóa để lưu trữ lượng lớn dữ liệu phi cấu trúc (images, videos, backups)
- Protocols: Amazon S3, OpenStack Swift, MinIO
- Ưu điểm:
    - Có khả năng mở rộng quy mô lớn và hiệu suất cao.
    - Có khả năng sao lưu, phục hồi dữ liệu tốt và đảm bảo an toàn dữ liệu.
    - Chi phí thấp hơn so với File Storage và Block Storage, nhất là với nhu cầu lưu trữ một lượng lớn dữ liệu.
    - Dễ dàng tích hợp, phân phối dữ liệu và quản lý metadata.
    - Phù hợp cho lưu trữ dữ liệu lớn, phân tán, sao lưu, phục hồi và các ứng dụng đám mây khác.
- Nhược điểm:
    - Không phù hợp cho tính toán và truy vấn dữ liệu nhanh, hiệu quả.
    - Có thể gặp độ trễ cao hơn khi truy cập dữ liệu.
    - Không thể khóa tệp, phân quyền truy cập hạn chế.
    - Khó khăn trong di chuyển dữ liệu.
- Giống như một nhà kho với các hộp được dán nhãn. Mỗi hộp (object) có một mã vạch (ID) và mô tả (metadata) riêng --> Có thể tra cứu đối tượng theo ID hoặc thẻ của nó.

<img width="1026" height="632" alt="image" src="https://github.com/user-attachments/assets/339bdd08-c77a-4b50-abde-a4a94f9ac1d0" />


# Cinder:
**Cinder** is the block storage service for OpenStack. It's designed to present storage resources to end users that can be consumed by the OpenStack compute project.
- Persitent block level storage devices for use with compute instances 
- Block Device Lifecycle Management: create, delete, extend or attach or detach,...
- One to one relationship between instances and block storage device: After plugging in your new drive, you can format it to NTFS, ext3 or whatever you need.
- Manages snapshoots
- Manages volume types: provide block storage or volumes with commodity hardware. If you wonder about what's going on behind the scenes, it's just LVM (Linux volume management) - Glusterfs and Ceph are also popular software based storage solutions.
- Fully integrated into Nova and Horizon allowing self service

### Volumes
- Persistent R/W Block storage
- Attached to instances as secondary storage
- Can be used as root volume to boot instances
- Volume lifecycle management
    - Create, delete, extend volumes
    - Attach/detach volumes
- Manages volume management

### Snapshoot
- A read only copy of a volume
- Create/delete snapshoots
- Create a volume out of a snapshot

### Backup
- Backup is an admin operation
- Done from CLI
- Backup stored in Swift: Needs a container
- Create/Restore backups
- $ cinder backup-create "volume-id"

### Quotas
- Quota can be set for operations limits
- Enforced per project:
    - Number of volumes
    - Storage space in GB
    - Number of snapshots
 
## Cinder Architecture:

<img width="1024" height="738" alt="image" src="https://github.com/user-attachments/assets/1eb0ae5d-4cd3-45e2-ab37-477949e72d4a" />

<img width="1384" height="797" alt="image" src="https://github.com/user-attachments/assets/cac9ec96-099d-4723-80eb-a683eb73acbf" />


- Cinder API is a wsgi application that accepts and validates rest based Json or XML requests from clients and routes them to other cinder processes as appropriate over the message bus. The operations possible through the API are:
    - Volume create/delete/list/show
    - Create from volume, image or snapshot
    - Snapshot create/delete/list/show
    - Volume attach/detach
    - support other operations related to volume types, quotas or backups
- Cinder-volume daemon đóng vai trò như là vùng chứa hoạt động cho tiến trình cinder và chấp nhận yêu cầu từ các tiến trình cinder khác như: cinder-scheduler,...
- The cinder volume service gửi phản hồi các yêu cầu đọc và ghi đến block storage service để duy trì trạng thái.
- Drivers contain backend specific code to communicate with various storage types.
    - Eg: Linux, LVM storage controllers from various vendors like EMC or NetApp or other distributed file systems.
    - It is possible to run multiple cinder-volume instances, each one with its own configuration file describing settings and the storage backend.
    - One cinder volume instance can manage multiple backends. Each backend driver is generally configured to interact with one storage pool.
- Cinder scheduler Daemon selects the optimal storage provider node on which to create the volume. It also has plugins for filters and weights.
- Cinder backup daemon provides backing up volumes of any type to a backup storage provider, which could be swift/ceph. Like the cinder volume service, it can interact with a variety of storage providers through a driver architecture.
- Messaging queue could be rabbitmq or amqp, and it routes information between the block storage processes.

** Example of Data and Control Traffic For Cinder

- Cinder mainly interacts with Nova to provide persistent volumes to instances which Nova manages. The way to do that is via interacting through the APIs of each component.
- Let's say an instance requires a volume. This means Nova and Cinder should talk through their rest APIs. After getting their request via its API, cinder builds and returns all of the info needed by Nova to attach the specified volume to the instance.
- Nova does a volume attachment and both services update their databases with the latest status.
- The important point I want to mention is the fact that the data path is over an iSCSI connection over the network data path and control path are completely different.

## Managing Cinder from CLI
- cinder service-list
- cinder service-disable
- cinder service-enable
- openstack command list | grep openstack.volume -A 40: see all cinder commands
- openstack volume create --size 1 vol 1: create volume with specify size (1BG name vol1)
- openstack volume list: list volumes
- openstack server list: list servers
- openstack server add volume demo1 vol1: server add volume vol1 to demo1 server

- openstack backup create --name backup1 --force vol1
- openstack volume backup show backup1

- openstack snapshot create --name snap1 --force vol1

** Others:
- sudo mkfs.etx3 /dev/vdc: format ....
- sudo mkdir /mydisk
- sudo mount /dev/vdc /mydisk
---> volume has been created and attched to an instances.


# Vocabulary for Cinder concept:
- Logical Volumes Manager (LVM)
- iSCSI Qualified Name (IQN): là format định dạng phổ biến cho tên iSCSI, dùng để xác định duy nhất các nút trong mạng iSCSI
    - Format: iqn.yyyy-mm.domain:identifier
    - Ex: iqn.2015-10.org.openstack.408ae959bce1
- NVMe Qualified Name (NQN): là format định dạng phổ biến cho tên NVMe, xác định duy nhất các máy chủ hoặc hệ thống con NVM trong mạng. Có 2 formats:
    - 1st format: nqn.yyyy-mm.domain:identifier
      Ex: nqn.2014-08.com.example:nvme:nvm-subsystem-sn-d78432
    - 2nd format: nqn.2014-08.org.nvmexpress:uuid:identifier
      Ex: nqn.2014-08.org.nvmexpress:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf6
 
# Clusion & Links:
[1] Openstack Installation Guide: https://docs.openstack.org/install-guide/

[2] Openstack Block Storage (Cinder): https://docs.openstack.org/cinder/latest/

[3] Openstack Cinder Github: https://github.com/openstack/cinder




















