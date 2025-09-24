# Ceph là gì?
## Ceph
**Ceph** là một hệ thống lưu trữ phân tán mã nguồn mở (open-source distributed storage system), cung cấp lưu trữ thống nhất cho (object storage, block storage, file system) với khả năng mở rộng cao, độ tin cậy và tự động hóa. Nó được thiết kế để chạy trên các nền tảng phần cứng thông thường, với quan điểm thiết kế các hệ thống lưu trữ - mở rộng đến hàng petabyte nhưng với chi phí rẻ, hợp lý.

Trong OpenStack – nền tảng đám mây mã nguồn mở quản lý tài nguyên compute, storage và networking – Ceph thường được sử dụng làm backend lưu trữ chính, thay thế cho các giải pháp lưu trữ truyền thống.

## Vai trò của Ceph trong các dịch vụ Openstack
Ceph hỗ trợ ba loại lưu trữ chính, phù hợp với các thành phần cốt lõi của OpenStack:

| Dịch vụ OpenStack | Loại lưu trữ Ceph | Mô tả |
| :---------------- | :---------------- | :-----|
| Glance (Image Service) | RBD (Block) hoặc Object (RGW) | Lưu trữ hình ảnh VM (images) dưới dạng Ceph Block Devices hoặc object storage. Cho phép clone nhanh chóng khi tạo VM. |
| Cinder (Block Storage) | RBD (Block) | Cung cấp volume lưu trữ block cho VM, hỗ trợ boot-from-volume và backup. |
| Swift (Object Storage) | RGW (Object Gateway) | Ceph RGW cung cấp giao diện S3/Swift-compatible cho lưu trữ object, tích hợp Keystone để xác thực. |
| Nova (Compute) | RBD (Block) | Lưu trữ trực tiếp disk VM trong Ceph, hỗ trợ live-migration và evacuate mà không cần Cinder (từ phiên bản Havana trở lên). |
| Manila (Shared File Systems) | CephFS (File) | Cung cấp file storage chia sẻ cho các workload cần POSIX-compliant. |
| Cinder Backup | RBD/Object | Backup volume vào pool riêng trong Ceph.|

Ceph hoạt động dựa trên RADOS (Reliable Autonomic Distributed Object Store), sử dụng thuật toán CRUSH để phân phối dữ liệu tự động, đảm bảo fault-tolerance và scalability.

## Lợi ích việc dùng Ceph trong Openstack
- **Scalability:** Dễ mở rộng theo nhu cầu OpenStack mà không cần phần cứng chuyên dụng/truyền thống (SAN và NAS).
- **Chi phí thấp:** Mã nguồn mở, không phụ thuộc vendor.
- **Tích hợp mượt mà:** Hỗ trợ Keystone cho authentication, và các API chuẩn (S3, Swift).
- **Hiệu suất cao:** Hỗ trợ live-migration nhanh, clone images tức thì, và phân phối dữ liệu đều.
- **Độ tin cậy:** Tự động replicate dữ liệu, chịu lỗi node mà không gián đoạn dịch vụ.

Tuy nhiên, Ceph thêm lớp trừu tượng cho block storage (không lý tưởng cho một số VM cần I/O cực cao), và khuyến nghị tách riêng cluster Ceph khỏi OpenStack để tránh ảnh hưởng hiệu suất.

## Nhược điểm / lưu ý
- Yêu cầu phần cứng tương đối lớn khi triển khai production
- Cần người quản trị có kinh nghiệm (Ceph khá phức tạp)
- Nếu cluster Ceph không được cấu hình tốt, có thể ảnh hưởng lớn đến toàn bộ OpenStack

# Ceph Architecture

<img width="693" height="491" alt="image" src="https://github.com/user-attachments/assets/f8b8057d-5fdf-48d0-bef0-ea4dd914e156" />

Ceph bao gồm các thành phần chính sau, hoạt động dựa trên RADOS (Reliable Autonomic Distributed Object Store):

| Thành phần | Vai trò |
| :--------- | :------ |
| RADOS | Lớp lưu trữ nền tảng, xử lý phân phối và nhân bản dữ liệu. |
| Monitor (MON) | Quản lý cluster map (CRUSH map, OSD map), duy trì trạng thái cluster. Thường triển khai số lẻ (3 hoặc 5) để đảm bảo quorum. |
| Object Storage Daemon (OSD) | Quản lý lưu trữ vật lý (disk), xử lý I/O dữ liệu. Mỗi OSD đại diện cho một disk hoặc phân vùng | 
| Metadata Server (MDS) | Quản lý metadata cho CephFS (file storage). Không cần cho RBD hoặc RGW. |
| RADOS Gateway (RGW) | Cung cấp giao diện RESTful (S3/Swift-compatible) cho object storage. |
| RBD (RADOS Block Device) | Cung cấp block storage cho VM/container, tích hợp với OpenStack/KVM. |
| CephFS | Cung cấp file system POSIX-compliant cho shared storage. |
| CRUSH Algorithm | Quy tắc phân phối dữ liệu thông minh, loại bỏ bottleneck và đảm bảo cân bằng tải. |

Cấu trúc tổng quan:

<img width="479" height="632" alt="image" src="https://github.com/user-attachments/assets/64ce0581-c8f0-4eeb-9a32-b37c9b567044" />


- **Client:** Các ứng dụng hoặc hệ thống (như OpenStack, Kubernetes) tương tác với Ceph qua giao diện RBD, RGW hoặc CephFS.
- **Pool:** Tập hợp logic của các object, chứa các **Placement Group (PG)**. Mỗi pool có thể cấu hình replication hoặc erasure coding.
- **Placement Group (PG):** Nhóm trung gian để quản lý object, giúp CRUSH phân phối dữ liệu đên OSD.
- **Cluster Map:** Bản đồ trạng thái cluster (MON, OSD, PG), được client sử dụng để biết dữ liệu nằm ở đâu.

# Ceph Workflow
Ceph sử dụng thuật toán **CRUSH** để phân phối dữ liệu mà không cần lookup tập trung, giúp tăng hiệu suất và khả năng mở rộng.

1. Client gửi request:
- Client (VD: OpenStack VM, ứng dụng S3) gửi yêu cầu đọc/ghi đến cluster Ceph thông qua giao diện RBD, RGW hoặc CephFS.
- Client cần biết cluster map (lấy từ Monitor) và file **ceph.conf**.
2. Xác định vị trí dữ liệu:
- Client sử dụng **CRUSH algorithm** để tính toán vị trí dữ liệu:
  - Dựa trên object ID, pool name và CRUSH map, xác định PG nào sẽ chứa dữ liệu.
  - PG được ánh xạ tới một tập hợp OSD ( thường 3 OSD cho replication)
3. Giao tiếp với OSD:
- Client gửi yêu cầu trực tiếp tới **Primary OSD** (OSD chịu trách nhiệm chính trong PG).
- Primary OSD phối hợp với các Replica OSD để đảm bảo dữ liệu được ghi đồng bộ (nếu dùng replication).
4. Xử lý ghi:
- Primary OSD ghi dữ liệu vào disk của mình và gửi bản sao tới các Replica OSD.
- Sau khi tất cả OSD xác nhận, Primary OSD trả về ACK cho client
5, Xử lý đọc:
- Client yêu cầu đọc từ Primary OSD, OSD trả về dữ liệu trực tiếp.
- Nếu Primary OSD lỗi, client tự động liên hệ với Replica OSD dựa trên CRUSH map.
6. Monitor cập nhật trạng thái:
- Monitor theo dõi trạng thái cluster, cập nhật cluster map khi có thay đổi (OSD thêm/xoá, lỗi node).
- Client định kỳ lấy cluster map mới để đảm bảo tính chính xác.

```sequence
[Client] 
  |
  v  (Gửi yêu cầu đọc/ghi)
[Monitor]  <-- Lấy Cluster Map (ceph.conf)
  |
  v  (Cập nhật trạng thái cluster)
[CRUSH Algorithm]  <-- Tính toán PG & OSD dựa trên Object ID
  |
  v  (Xác định vị trí)
[Primary OSD]  <-- Xử lý chính (ghi/đọc dữ liệu)
  |  
  +--> [Replica OSD 1]  <-- Phối hợp replicate (cho ghi)
  |  
  +--> [Replica OSD 2]  <-- Xác nhận ACK từ replicas
  |  
  +--> [Replica OSD N]  <-- (Nếu lỗi Primary, chuyển sang Replica)
  |
  v  (Trả về ACK/Data)
[Client]  <-- Phản hồi cuối cùng (thành công/lỗi)
```

## Ceph Network

<img width="818" height="363" alt="image" src="https://github.com/user-attachments/assets/8ca43202-c08d-4079-adbf-ef5fdfa10cf6" />

<img width="979" height="656" alt="image" src="https://github.com/user-attachments/assets/26228394-8316-4548-9350-5a1622f10eba" />

## BLuestore vs filestore

## Các bước triển khai Ceph lên Openstack
1. Triển khai cluster Ceph
2. Tạo các pool trong Ceph cho các dịch vụ OpenStack
3. Cấu hình các dịch vụ OpenStack sử dụng Ceph làm backend (Cinder, Glance, Nova...)
4. Kiểm tra hoạt động và validate tích hợp
5. Tinh chỉnh hiệu suất và giám sát

**Example:**
1. Cách 1: Dùng Ceph-Ansible (phổ biến, ổn định)
- Yêu cầu: 1 máy chủ admin, các node Ceph (mon, osd, mgr...)
- Repo: ceph/ceph-ansible

Ví dụ inventory:
```bash
[mons]
mon1 ansible_host=192.168.1.10

[osds]
osd1 ansible_host=192.168.1.11
osd2 ansible_host=192.168.1.12

[mgrs]
mgr1 ansible_host=192.168.1.10
```

Lệnh chạy:
```bash
ansible-playbook site.yml -i inventory
```

* Cách 2: Dùng cephadm (Ceph >= Octopus)
```bash
cephadm bootstrap --mon-ip 192.168.1.10
ceph orch host add node1 192.168.1.11
ceph orch apply osd --all-available-devices
```

2. Tạo các Ceph Pool cho OpenStack
```bash
ceph osd pool create volumes 128
ceph osd pool create images 128
ceph osd pool create vms 128
```

Nếu dùng erasure coding:
```bash
ceph osd erasure-code-profile set myprofile k=2 m=1
ceph osd pool create volumes 128 128 erasure myprofile
```

3. Tạo keyring cho OpenStack services
```bash
ceph auth get-or-create client.glance mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=images'
ceph auth get-or-create client.cinder mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=volumes'
ceph auth get-or-create client.nova mon 'allow r' osd 'allow rwx pool=vms, allow rwx pool=volumes'
```

Sau đó lưu các key vào file trên các node OpenStack:
```bash
/etc/ceph/ceph.client.cinder.keyring
/etc/ceph/ceph.client.glance.keyring
/etc/ceph/ceph.client.nova.keyring
```

4. Cấu hình các dịch vụ OpenStack dùng Ceph
- Glance – lưu image lên Ceph:
`/etc/glance/glance-api.conf`:

```bash
[glance_store]
stores = rbd
default_store = rbd
rbd_store_pool = images
rbd_store_user = glance
rbd_store_ceph_conf = /etc/ceph/ceph.conf
```

- Cinder – backend lưu trữ block (volume):
`/etc/cinder/cinder.conf`:

```bash
[DEFAULT]
enabled_backends = ceph

[ceph]
volume_driver = cinder.volume.drivers.rbd.RBDDriver
rbd_pool = volumes
rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_user = cinder
backend_host = ceph
volume_backend_name = ceph
```

- Nova – boot từ Ceph volume:
`/etc/nova/nova.conf`:

```bash
[libvirt]
images_type = rbd
images_rbd_pool = vms
images_rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_user = nova
rbd_secret_uuid = <uuid bạn tạo bằng virsh>
```

Tạo secret cho libvirt:
```bash
virsh secret-define --file secret.xml
virsh secret-set-value --secret <uuid> --base64 $(ceph auth get-key client.nova) --config
```

### Một số lưu ý khi triển khai thực tế

| Lưu ý      | Chi tiết                                                     |
| ---------- | ------------------------------------------------------------ |
| Network    | Ceph nên có mạng riêng (cluster network) để giảm tải         |
| Disk       | SSD cho journal/wal (nếu dùng BlueStore) cải thiện hiệu năng |
| Pool       | Tính toán PG số lượng phù hợp để tránh over/under mapping    |
| Auth       | Sử dụng `cephx` để bảo mật                                   |
| Monitoring | Cài đặt `ceph dashboard` hoặc dùng Prometheus, Grafana       |













