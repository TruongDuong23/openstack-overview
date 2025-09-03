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


------------------------------------------------------------------------------------------------------------------------


# Cinder:
**Cinder** là 1 module có dịch vụ cung cấp block storage trong OpenStack. Nó được thiết kế để thể hiện tài nguyên lưu trữ đã sử dụng tới người dùng bởi OpenStack compute.
- Persitent block storage được gắn vào compute instances/VMs. 
- Quản lý vòng đời Block Storage: create, delete, extend or attach or detach,...
- Mối quan hệ giữa instances and block storage device: Sau khi cắm vào drive mới, có thể format nó thành NTFS, ext3 or whatever you need.
- Quản lý snapshoots và các loại volumes
- Hoàn toàn tích hợp vào Nova và Horizon

## Một số hình thức lưu trữ trong Openstack

|             |Lưu trữ tạm thời|Block Storage|Object Storage|
|-------------|----------------|-------------|--------------|
|Hình thức sử dụng |Dùng để chạy hệ điều hành và scrath space|Thêm một persistent storage vào VM|lưu trữ các VM iamge , disk volume , snapshot VM,....|
|Hình thức truy cập|Qua một file system|Một Block device có thể là một partition, formated, mounted (giống như là : /dev/vdc)|Thông qua RESTAPI|
|Có thể truy cập từ|Trong một VM|Trong một VM|Bất kỳ đâu|
|Quản lý bỏi|NOVA|Cinder|Swift|
|Những vấn đề tồn tại|VM được kết thúc|Có thể được xóa bởi người dùng|Có thể được xóa bởi user|
|Kích cỡ được xác định bởi|Người quản trị hệ thống cấu hình cài đặt kích cỡ, tương tự như là Flavors|Dựa theo đặc điểm yêu cầu của người dùng|Số lượng lưu trữ mà máy vật lý hiện có|
|Ví dụ điển hình|10GB đĩa thứ nhất, 30GB đĩa thứ 2|1TB disk|10s of TBs of dataset storage|

## Cinder Architecture:

<img width="1384" height="797" alt="image" src="https://github.com/user-attachments/assets/cac9ec96-099d-4723-80eb-a683eb73acbf" />


- **Cinder-api:** là 1 ứng dụng wsgi, nó cho phép và xác thực thông qua rest API dựa trên yêu cầu được viết bằng file Json hoặc XML từ người dùng và truyền chúng đến các cinder processes khác thông qua message bus (RabbitMQ/AMQP). Các tiền trình có thể cử lý thông qua API:
    - Volume create/delete/list/show
    - Create from volume, image or snapshot
    - Snapshot create/delete/list/show
    - Volume attach/detach
    - support other operations related to volume types, quotas or backups
- **Cinder-scheduler:** sử dụng plugins có cơ chế filters và weights để lựa chọn backend storage tốt nhất dựa trên các tiêu chí như dung lượng khả dụng, hiệu suất.
    - Key Filters: CapacityFilter (kiểm tra free space), CapabilityFilter ( matches backend capabilities),...
- **Cinder-volume:** quản lý thiết bị block storage và điều phối tới storage backend. Thực hiện tiến trình như: create, delete, attach, detach,... volumes, snapshots,... bằng cách tương tác với storage backend đã được cấu hình thông qua giao thức Fibre Channel.
- **Cinder-backup:** Xử lý quá trình backup và sao lưu của volume. Tạo backups volumes vào hệ thống backup storage (cụ thể là CEPH) và khôi phục chúng khi cần.
- **Storage Backend Drivers:** gồm backend code cụ thể để giao tiếp với nhiều loại hệ thống storage. Eg: CEPH
    - Có thể chạy nhiều cinder-volume instances, mỗi driver có file cấu hình riêng về settings và storage backend.
    - 1 cinder-volume instance có thể quản lý nhiều backends. Mỗi backend driver được cấu hình để tương tác với 1 nhóm lưu trữ *
- **Database**: lưu trữ metadata về volumes, snapshots, backups và trạng thái của chúng. Nhằm duy trì bản ghi liên tục về tài nguyên và cấu hình của chúng.

## Cinder Workflow

1. Volume Creation:
- Gửi request tạo 1 volume qua Cinder-API (e.g., dùng Horizon hoặc CLI).
- API xác thực request và truyền nó đến message queue.
- Cinder-Scheduler nhận request, đánh giá và lựa chọn storage backends tốt nhất bằng cách sử dụng filters và weighers.
- Scheduler gửi request đến Cinder-Volume service và chạy trên storage backend đã được chọn.
- Cinder-Volume sử dụng storage driver phù hợp để tạo volume và updates database về metadata của volume.
- API trả kết quả về user.


## Cinder & Nova Workflow

[](https://github.com/hocchudong/ghichep-OpenStack/blob/master/images/cinder/cinder-nova-workflow.png)<img width="924" height="520" alt="image" src="https://github.com/user-attachments/assets/278eb2a7-d2e1-4448-83d8-6c3ab49fd721" />

1. Client yêu cầu gán volume thông qua Nova REST API (Client có thể sử dụng tiện ích CLI của python-novaclient)
2. Nova-api thực hiện quá trình xác nhận yêu cầu và thông tin người dùng. Sau khi xác thực, gọi API Cinder để có được thông tin kết nối cho volume được xác định.
3. Cinder-api thực hiện quá trình xác nhận yêu cầu hợp lệ và thông tin người dùng hợp lệ. Sau khi xác nhận, 1 message sẽ được gửi đến quản lý volume thông qua AMQP.
4. Cinder-volume tiến hành đọc message từ hàng đợi, gọi Cinder driver tương ứng với volume được gắn vào.
5. NetApp Cinder driver chuẩn bị Cinder Volume chuẩn bị cho việc gán volume (các bước cụ thể phụ thuộc vào giao thức lưu trữ được sử dụng)
6. Cinder-volume thực hiện quá gửi thông tin phản hồi đến cinder-api thông qua hàng đợi AMQP.
7. Cinder-api thực hiện quá trình đọc message phản hồi từ cinder-volume từ hàng đợi; Truyền thông tin kết nối đến RESTful phản hồi tới NOVA
8. Nova tạo ra kết nối với bộ lưu trữ thông tin được trả về Cinder.
9. Nova truyền volume device/file tới hypervisor, sau đó gắn volume device/file vào máy ảo client như 1 block device thực thể hoặc ảo hoá (phụ thuộc giao thức lưu trữ)  [6]

## Example of Data and Control Traffic For Cinder / Cinder Attach Workflow

- Cinder tương tác trực tiếp với Nova để cung cấp persistent block storage volumes, snapshots và backups tới instances nơi mà Nova quản lý bằng cách call API.
- Let's say an instance requires a volume. This means Nova and Cinder should talk through their rest APIs. After getting their request via its API, cinder builds and returns all of the info needed by Nova to attach the specified volume to the instance.
- Nova does a volume attachment and both services update their databases with the latest status.
- The important point I want to mention is the fact that the data path is over an iSCSI connection over the network data path and control path are completely different.

<img width="1176" height="891" alt="image" src="https://github.com/user-attachments/assets/879c4fff-289a-4129-989f-0e71260578c4" />

<img width="740" height="551" alt="image" src="https://github.com/user-attachments/assets/4fdef1bd-0ee3-4194-a47f-638d2e22f2be" />

- Cinder gọi Cinder qua APi của cinder, truyền thông tin kết nối.
  - Ví vụ :  Host name, iSCSI initiator name, FC WWPNs
- Cinder-API chuyển thông điệp đến Cinder-volume.
- Sau đó trình kiểm tra lỗi đầu vào sẽ làm việc và gọi đến volume driver.
- Volume Driver sẽ chuẩn bị các yếu tố cần thiết để cho phép kết nối.
  - Ví dụ : Cho phép máy chủ NOVA có thể truy cập vào Volume.
- Volume driver trả về thông tin kết nối, được truyền cho NOVA.
  - Ví dụ : iSCSI iqn and portal, FC WWPN.
- NOVA tạo kết nối đến storage sử dụng thông tin được trả về.
- NOVA chuyển volume device/file tới hypervisor.

## Cinder Status

|Status|Mô tả|
|------|-----|
|Creating|Volume được tạo ra|
|Available|Volume ở trạng thái sẵn sàng để attach vào một instane|
|Attaching|Volume đang được gắn vào một instane|
|In-use|Volume đã được gắn thành công vào instane|
|Deleting|Volume đã được xóa thành công|
|Error|Đã xảy ra lỗi khi tạo Volume|
|Error deleting|Xảy ra lỗi khi xóa Volume|
|Backing-up|Volume đang được back up|
|Restore_backup|Trạng thái đang restore lại trạng thái khi back up|
|Error_restoring|Có lỗi xảy ra trong quá trình restore|
|Error_extending|Có lỗi xảy ra khi mở rộng Volume|


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
- Block Storage:
    - Block Storage : hay còn được gọi là Volume storage được gắn vào các VMs dưới dạng volumes . Trong OpenStack Cinder là mã phần mềm triển khai block storage.
Các volumes này là "persistent" có nghĩa là các volume này có thể gán cho 1 instane rồi gỡ bỏ và gán cho instane khác mà vẫn giữ nguyên dữ liệu. Các loại Block 
Storage cho phép các instane truy cập trực tiếp đến phần cứng storage của thiết bị thật , việc này giúp tăng hiệu suất đọc/ghi I/O  [4]
    - Các volume có vòng đời phụ thuộc vào thời gian sống của VM.
    - Tương tự như Amazon Elastic Block Store (EBS)
- AMQP:
    - RabbitMQ là một message broker ( message-oriented middleware) sử dụng giao thức AMQP - Advanced Message Queue Protocol (Đây là giao thức phổ biến, thực tế rabbitmq hỗ trợ nhiều giao thức). RabbitMQ được lập trình bằng ngôn ngữ Erlang. RabbitMQ cung cấp cho lập trình viên một phương tiện trung gian để giao tiếp giữa nhiều thành phần trong một hệ thống lớn ( Ví dụ openstack).
    -  RabbitMQ sẽ nhận message đến từ các thành phần khác nhau trong hệ thống, lưu trữ chúng an toàn trước khi đẩy đến đích.
- iSCSI:
    - Trong hệ thống mạng máy tính, iSCSI (viết tắt của internet Small Computer System Interface) dựa trên giao thức mạng internet (IP) để kết nối các cơ sở dữ liệu.
    - Nói một cách đơn giản nhất, iSCSI sẽ giúp tạo 1 ổ cứng Local trong máy tính của bạn với mọi chức năng y như 1 ổ cứng gắn trong máy tính vậy. Chỉ khác ở chỗ dung 
lượng thực tế nằm trên NAS và do NAS quản lý. [5]


------------------------------------------------------------------------------------------------------------------------------


# Neutron [8]
## Openstack Networking
- Neutron - code name Openstack Networking là 1 module trong Openstack để quản lý mạng và địa chỉ IP trong 1 Openstack-based cloud. OpenStack Network cung cấp API để người dùng hay các yêu cầu chuyển tới thực thi cấu hình network và xử lý khác. Người dùng có thể định nghĩa các kết nối mạng riêng trong cloud.
- OpenStack Networking services có thể được tách ra nhiều host để cung cấp khả năng đàn hồi và dự phòng.
## Các đặc tính của OpenStack Networking
OpenStack Networking gồm rất nhiều công nghệ trọng datacenter:
- switching
- routing
- load balancing
- firewall
- VPN
## Physical Infrastructure
Khi thiết kế một network, quan trọng nhất là xác định cách thức cloud sẽ làm việc. Có yêu cầu về việc mở rộng, dự phòng không?

[](https://github.com/TrongTan124/ghichep-OpenStack/blob/master/Images/read-v2-1.png)<img width="1071" height="615" alt="image" src="https://github.com/user-attachments/assets/e5820f0f-f271-42e4-81f4-d6a8e199c25a" />

## Các loại network traffic
Có ít nhất 4 loại traffic có thể nhìn thấy được:
- Management
- API
- External
- Guest
## Basic networking elements in Neutron
Sử dụng Neutron API, người dùng có thể tạo network với các thành phần:
- Network
- Subnet
- Port
## Mở rộng chức năng với plugins:
Có 2 loại plugin trong kiến trúc Neutron
- Core plugin:
    - ml2
- Service plugin:
    - router
    - load balancer
    - firewall
    - virtual private network
 
## ML2 architecture
Mô hình ở mức high level cách thức tương tác giữa các thành phần trong Neutron

<img width="844" height="685" alt="image" src="https://github.com/user-attachments/assets/e8e9236d-efec-4bdc-882f-8461caa97e88" />

### Configure the Neutron metadata agent
- OpenStack cung cấp metadata service để người dùng nhận các thông tin về instance. Các thông tin này được sử dụng để cấu hình hoặc quản lý instance. Metadata gồm nhiều thông tin như: hostname, fixed, floating IP, public keys,...
- Instance truy cập vào metadata thông qua HTTP tại địa chỉ http://169.254.169.254 trong suốt quá trình khởi động

<img width="910" height="738" alt="image" src="https://github.com/user-attachments/assets/eff2ee43-fa18-4622-86ee-03c38a030eb5" />

1. Instance gửi yêu cầu metadata tới 169.254.169.254 thông qua HTTP khi khởi động
2. Yêu cầu metadata gửi tới router hoặc DHCP namespace
3. Metadata proxy service trong namespace gửi yêu cầu tới Neutron metadata thông qua Unix socket
4. Neutron metadata gửi yêu cầu tới Nova metadata API service
5. Nova metadata API service phản hồi lại yêu cầu tới Neutron metadata
6. Neutron metadata gửi trả lại phản hồi tới metadata proxy trong namespace
7. metadata proxy service gửi trả lại phản hồi tới instance thông qua HTTP
8. Instance nhận thông tin metadata và tiếp tục khởi động

 
# Clusion & Links:
[1] Openstack Installation Guide: https://docs.openstack.org/install-guide/

[2] Openstack Block Storage (Cinder): https://docs.openstack.org/cinder/latest/

[3] Openstack Cinder Github: https://github.com/openstack/cinder

[4] Types of Storage in Openstack: (http://blogit.edu.vn/cac-loai-storage-trong-openstack/)

[5] iSCSI: http://ducquang415.com/view-45717/iscsi-la-gi-gioi-thieu-cach-map-o-cung-nas-thanh-1-o-cung-local/

[6] Cinder Process: http://netapp.github.io/openstack-deploy-ops-guide/icehouse/content/section_cinder-processes.html

[7] Openstack Summary: https://github.com/hocchudong/ghichep-OpenStack/tree/master

[8] Openstack Neutron: https://github.com/TrongTan124/ghichep-OpenStack/blob/master/neutron/read-Learning-OpenStack-Networking-(Neutron)%2C2nd-Edition.md


















