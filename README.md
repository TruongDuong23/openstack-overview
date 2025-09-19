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


---


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

## Key features of Openstack Cinder:

<img width="881" height="456" alt="Screenshot 2025-09-18 at 19 03 27" src="https://github.com/user-attachments/assets/7ca7108b-1258-410d-84bf-67390e8e14cb" />

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


---


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
- Core plugin: chịu trách nhiệm tương thích với logical network API để có thể thực thi bằng L2 agent và IP Address Management (IPAM) chạy trên host.
    - ml2
- Service plugin:
    - router
    - load balancer
    - firewall
    - virtual private network
 
## ML2 architecture
Mô hình ở mức high level cách thức tương tác giữa các thành phần trong Neutron

<img width="844" height="685" alt="image" src="https://github.com/user-attachments/assets/e8e9236d-efec-4bdc-882f-8461caa97e88" />

ML2 plugin dựa vào các loại khác nhau của driver để xác định loại network thực thi và cơ chế để thực thi
- **Type drivers**: mô tả các loại mạng được hỗ trợ bởi Neutron. Eg: FLAT, VLAN, VXLAN, GRE.
- **Mechanism drivers**: được sử dụng để thực thi mạng được chỉ ra trong phần mềm
- **L2 agent**: xây dựng và vận hành hạ tầng mạng ảo
- **L3 agent**: chịu trách nhiệm xây dựng và vận hành Neutron router cùng các chức năng liên quan

<img width="524" height="574" alt="image" src="https://github.com/user-attachments/assets/da86bf64-b195-4b84-aea5-127fb9f3fa20" />

1. Neutron nhận yêu cầu kết nối máy ảo tới mạng. API server gọi tới ML2 plugin  để xử lý yêu cầu.
2. ML2 plugin chuyển tiếp yêu cầu tới OVS mechanism driver để tạo message sử dụng thông tin có sẵn trong request. Message được gửi cho OVS agent tương ứng xử lý thông qua quản lý network
3. OVS agent nhận message và cấu hình trên local virtual switch.
4. Trong lúc đó, DHCP agent cũng nhận message tương ứng của yêu cầu này và cấu hình DHCP server trên network node. Sau khi xong, virtual machine instance sẽ giao tiếp với DHCP server và nhận địa chỉ IP thông qua dữ liệu mạng.

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

----

## Networks

Network là đối tượng trung tâm của Neutron v2.0 API data model và chỉ ra một **Layer 2** segment riêng biệt. Trong hạ tầng truyền thống, máy tính được kết nối tới switch port và nhóm cùng nhau thành **Virtual Local Area Networks (VLANs)** định danh bởi IDs duy nhất. Các máy tính trong một mạng hay VLAN có thể kế nối với nhau và không thể kết nối ra  các VLAN khác khi thiếu router. Hình sau giải thích cách mà network được tách biệt với nhau trong hạ tầng mạng truyền thống.

<img width="868" height="439" alt="image" src="https://github.com/user-attachments/assets/5c78c187-c70e-48ca-ba03-653a3827888a" />

### Network attributes

Bảng sau chỉ ra các thuộc tính cơ bản của network object:

<img width="874" height="437" alt="image" src="https://github.com/user-attachments/assets/776f590e-774c-457d-8498-f4ecab38a5ce" />

Network được liên kết với tenants hoặc project, có thể sử dụng bởi người dùng là thành viên của cùng tenant hay project. Network có thể được chia sẻ với các project khác hay subnet của project sử dụng tính năng **Role Based Access Control (RBAC)** của Neutron.

### Provider attributes

Provider network extension ánh xạ virtual network và physical network bằng cách thêm các thuộc tính mạng như network type, segmentation ID và physical interface. 

<img width="873" height="322" alt="image" src="https://github.com/user-attachments/assets/480d7517-f24e-4450-a121-bee9dfc0de4f" />

Tất cả các network đều có thuộc tính provider. Tuy nhiên, vì thuộc tính provider chỉ rõ cấu hình và ánh xạ mạng cụ thể, chỉ người dùng có admin role có thể chỉ định chúng khi tạo mạng. Người dùng không có admin role có thể tạo network nhưng Neutron server, không phải người dùng, sẽ quyết định loại mạng được tạo và mọi interface hay segmentation ID phù hợp.

==> Provider network chỉ có thể được tạo và quản lý bởi người quản trị OpenStack khi họ yêu cầu hiểu biết cấu hình hạ tầng mạng vật lý để cấu hình switch port phù hợp. Khi provider được tạo, người quản trị phải chỉ định rõ các thuộc tính đối với network. Provider network thường được cấu hình ở flat hoặc vlan, và sử dụng một thiết bị routing bên ngoài cho phép route lưu lượng phù hợp vào hoặc ra khỏi cloud.

    Còn Tenant network, không như provider network, được tạo bởi người dùng và được phân tách với các mạng khác trong cloud. Không cần phải cấu hình hạ tầng vật lý, các tenant kết nối các network qua Neutron router khi kết nối ra ngoài được yêu cầu.

----

## Subnets

Trong mô hình Neutron data, một subnet là một khối địa chỉ IPv4 hoặc IPv6 có thể gán cho máy ảo và tài nguyên mạng khác. Mỗi subnet phải có một subnet mask đại diện bởi một **Classless Inter-Domain Routing (CIDR)** và phải liên kết với một network:

<img width="868" height="275" alt="image" src="https://github.com/user-attachments/assets/6cdc47c9-ef5b-4b5d-b910-0bf92b692224" />

Trong hình trên, 3 VLAN riêng biệt tương ứng với các subnet. Instance và các thiết bị khác không thể gắn vào network khi thiếu subnet cụ thể. Instance kết nối tới một network có thể kết nối với nhau nhưng không thể kết nối tới mạng khác hoặc subnet không sử dụng router.

<img width="867" height="296" alt="image" src="https://github.com/user-attachments/assets/c37fd5bc-c4ac-4869-8cab-ed517b1572ec" />

----

## Port

Trong mô hình Neutron data, một port đại diện cho một switch port trên một logical switch, triển khai trên toàn bộ cloud và chưa thông tin về thiết bị kết nối. **Virtual machine interfaces (VÍ)** và các đối tượng network khác như router và DHCP server interface được ánh xạ tới Neutron port. Các port định nghĩa cả địa chỉ MAC và địa chỉ IP được gán cho thiết bị liên kết với chúng. Mỗi port phải được kết nối với một Neutron network

Hình sau chỉ ra cách một port gắn với Layer 2 trong mô hình OSI:

<img width="868" height="282" alt="image" src="https://github.com/user-attachments/assets/5b56ac75-2f32-4786-b2fa-9d880054f2dd" />

Khi Neutron được cài lần đầu, không có port nào tồn tại trong database. Khi network và subnet được tạo, port có thể được tạo với mỗi DHCP server tương ứng với mô hình logical switch sau:

<img width="421" height="293" alt="image" src="https://github.com/user-attachments/assets/aae348f1-80a5-4588-8883-ee455cd625ac" />

Khi một Instance được tạo, một port được tạo với mỗi network interface gắn với instance:

<img width="760" height="309" alt="image" src="https://github.com/user-attachments/assets/fd3eb9a6-f1ef-4a39-ad9b-3095a22498fd" />

Một port chỉ có thể được kết nối với một network. Do đó, nếu một instance được kết nối tới nhiều networks, nó sẽ được liên kết với nhiều port. Khi instance và tài nguyên cloud được khởi tạo, logical switch có thể mở rộng tới hàng trăm, hàng nghìn port

<img width="869" height="345" alt="image" src="https://github.com/user-attachments/assets/cda23620-aaa1-47bc-b114-1f96cc4f5b64" />

Không có giới hạn số port có thể được tạo trong Neutron. Tuy nhiên, tồn tại một quota giới hạn số port cho một tenant có thể tạo. Khi số port Neutron mở rộng, hiệu xuất của Neutron API server và thực thi của mạng trong cloud có thể giảm. Ý tưởng hay nhất là giữa quota tại điểm mà đảm bảo hiệu năng cloud, nhưng quota mặc định và subsequent nên tăng hợp lý.

----

## The Neutron workflow

Theo workflow Neutron chuẩn, network phải được tạo đầu tiên, theo đó là subnet và port.

### Booting an instance

Trước khi một instance có thể được tạo, nó phải liên kết với một network có subnet phù hợp hoặc một port tạo trước đã liên kết với một network. Luồng xử lý sau chỉ ra các bước liên quan trong khi khởi động một instance và gắn nó vào một network:

1. Người dùng tạo một network
2. Người dùng tạo một subnet và liên kết nó với network
3. Người dùng khởi động máy ảo và network chỉ định
4. Nova interface với Neutron tạo một port trên network
5. Neutron gán một địa chỉ MAC và địa chỉ IP tới port vừa tạo sử dụng các thuộc tính định nghĩa bởi subnet
6. Nova dựng instance với file XML bằng libvirt, chứa thông tin local network bridge và địa chỉ MAC. Khởi động instance.
7. Instance gửi một DHCP request trong khi boot, DHCP respond với địa chỉ IP tương ứng tới địa chỉ MAC của instance.

Nếu nhiều network interface được gắn vào một instance, mỗi network interface sẽ liên kết với một Neutron port duy nhất và có thể gửi DHCP request để nhận thông tin mạng tương ứng.

**Làm cách nào mô hình logical được thực thi**

Neutron agent là dịch vụ trên network và compute node, chịu trách nhiệm trao đổi thông tin mô tả bởi network, subnet và port; sử dụng nó để thực thi hạ tầng mạng ảo và thật.

Trong Neutron database, mối quan hệ giữa network, subnet và port được nhìn thấy như sau:

<img width="868" height="309" alt="image" src="https://github.com/user-attachments/assets/495120b2-1cd4-4a1d-a53c-9d81936a32b5" />

Thông tin này khi được thực thi trên compute node trong phạm vi của network interface ảo, switch hoặc bridge ảo và địa chỉ IP, theo hình sau:

<img width="695" height="546" alt="image" src="https://github.com/user-attachments/assets/b33494db-bc7d-4276-9130-98a8e3a66b49" />

Trong ví dụ trên, instance được kết nối tới network bridge trên compute node, bridge cung cấp kết nối từ instance tới physical network. 

### Deleting an instance

Đoạn sau mô tả các bước liên quan khi xóa một instance
1. Người dùng xóa instance
2. Nova interface với Neutron xóa port liên kết với instance
3. Nova xóa dữ liệu instance
4. Địa chỉ IP và MAC đã cấp phát được trả về cho pool

Khi instance được xóa, Neutron gỡ tất cả các kết nối virtual network từ compute node liên quan và gỡ thông tin port liên quan từ database.


---
 
# Clusion & Links:
[1] Openstack Installation Guide: https://docs.openstack.org/install-guide/

[2] Openstack Block Storage (Cinder): https://docs.openstack.org/cinder/latest/

[3] Openstack Cinder Github: https://github.com/openstack/cinder

[4] Types of Storage in Openstack: (http://blogit.edu.vn/cac-loai-storage-trong-openstack/)

[5] iSCSI: http://ducquang415.com/view-45717/iscsi-la-gi-gioi-thieu-cach-map-o-cung-nas-thanh-1-o-cung-local/

[6] Cinder Process: http://netapp.github.io/openstack-deploy-ops-guide/icehouse/content/section_cinder-processes.html

[7] Openstack Summary: https://github.com/hocchudong/ghichep-OpenStack/tree/master

[8] Openstack Neutron: https://github.com/TrongTan124/ghichep-OpenStack/blob/master/neutron/read-Learning-OpenStack-Networking-(Neutron)%2C2nd-Edition.md


















