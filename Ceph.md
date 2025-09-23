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












