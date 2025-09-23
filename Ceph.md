# Ceph là gì?
## Ceph
**Ceph** là một hệ thống lưu trữ phân tán mã nguồn mở (open-source distributed storage system), cung cấp lưu trữ thống nhất cho (object storage, block storage, file system) với khả năng mở rộng cao, độ tin cậy và tự động hóa. Trong OpenStack – nền tảng đám mây mã nguồn mở quản lý tài nguyên compute, storage và networking – Ceph thường được sử dụng làm backend lưu trữ chính, thay thế cho các giải pháp lưu trữ truyền thống.

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












