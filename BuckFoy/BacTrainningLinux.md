### Mục lục
[    REPORT 1     ](#Report)
- [1.1. FILE SYSTEM CONFIGURATION](#filesystem)
- [1.2. USER ACCOUNT MANAGEMENT](#useraccount)
- [1.3. PACKAGE MANAGEMENT](#package)
- [    REPORT 2    ](#report2)
#                    REPORT 1

##   1.1. FILE SYSTEM CONFIGURATION

File system được dùng để quản lý các dữ liệu được đọc và lưu trên thiết bị.File system cho phép người dùng truy cập nhanh chóng và an toàn khi cần thiết.Các loại file system

Filesystem cơ bản: EXT2, EXT3, EXT4, XFS, Btrfs, JFS, NTFS,…

Filesystem dành cho dạng lưu trữ Flash: thẻ nhớ,…

Filesystem dành cho hệ cơ sở dữ liệu

Filesystem mục đích đặc biệt: procfs, sysfs, tmpfs, squashfs, debugfs,



Thư mục
Chức năng
/bin
Các chương trình cơ bản
/boot
Chứa nhân Linux để khởi động và các file system maps cũng như các file khởi động giai đoạn hai.
/dev
Chứa các tập tin thiết bị (CD Rom, HDD, FDD….).
/etc
Chứa các tập tin cấu hình hệ thống.
/home
Thư mục dành cho người dùng khác root.
/lib
Chứa các thư viện dùng chung cho các lệnh nằm trong /bin và /sbin. Và thư mục này cũng chứa các module của kernel.
/mnt hoặc /media
Mount point mặc định cho những hệ thống file kết nối bên ngoài.
/opt
Thư mục chứa các phần mềm cài thêm.
/sbin
Các chương trình hệ thống
/srv
Dữ liệu được sử dụng bởi các máy chủ lưu trữ trên hệ thống.
/tmp
Thư mục chứa các file tạm thời.
/usr
Thư mục chứa những file cố định hoặc quan trọng để phục vụ tất cả người dùng.
/var
Dữ liệu biến được xử lý bởi daemon. Điều này bao gồm các tệp nhật ký, hàng đợi, bộ đệm, bộ nhớ cache,…
/root
Các tệp cá nhân của người quản trị (root)
/proc
Sử dụng cho nhân Linux. Chúng được sử dụng bởi nhân để xuất dữ liệu sang không gian người dùng.

