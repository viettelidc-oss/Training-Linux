### Mục lục
[    REPORT 1     ](#Report)
- [1.1. FILE SYSTEM CONFIGURATION](#filesystem)
- [1.2. USER ACCOUNT MANAGEMENT](#useraccount)
- [1.3. PACKAGE MANAGEMENT](#package)
[    REPORT 2    ](#report2)
#                    REPORT 1

##   1.1. FILE SYSTEM CONFIGURATION

File system được dùng để quản lý các dữ liệu được đọc và lưu trên thiết bị.File system cho phép người dùng truy cập nhanh chóng và an toàn khi cần thiết.Các loại file system

Filesystem cơ bản: EXT2, EXT3, EXT4, XFS, Btrfs, JFS, NTFS,…

Filesystem dành cho dạng lưu trữ Flash: thẻ nhớ,…

Filesystem dành cho hệ cơ sở dữ liệu

Filesystem mục đích đặc biệt: procfs, sysfs, tmpfs, squashfs, debugfs,



Thư mục | Chức năng |
----------|---------------|
/bin | Các chương trình cơ bản|
/boot| chứa nhân Linux để khởi động và các file system maps cũng như các file khởi động giai đoạn hai.|
/dev |  các tập tin thiết bị (CD Rom, HDD, FDD….).|
/etc | Chứa các tập tin cấu hình hệ thống.|
/home|Thư mục dành cho người dùng khác root.|
/lib|Chứa các thư viện dùng chung cho các lệnh nằm trong /bin và /sbin. Và thư mục này cũng chứa các module của kernel.|
/mnt hoặc /media| Mount point mặc định cho những hệ thống file kết nối bên ngoài.|
/opt| Thư mục chứa các phần mềm cài thêm.|
/sbin| Các chương trình hệ thống|
/srv| Dữ liệu được sử dụng bởi các máy chủ lưu trữ trên hệ thống.|
/tmp |Thư mục chứa các file tạm thời.|
/usr | Thư mục chứa những file cố định hoặc quan trọng để phục vụ tất cả người dùng.|
/var | Dữ liệu biến được xử lý bởi daemon. Điều này bao gồm các tệp nhật ký, hàng đợi, bộ đệm, bộ nhớ cache,…|
/root | Các tệp cá nhân của người quản trị (root) |
/proc | Sử dụng cho nhân Linux. Chúng được sử dụng bởi nhân để xuất dữ liệu sang không gian người dùng. |

### thông tin về hệ thống
cat /proc/cpuinfo  |thông tin về hệ thống |
cat /proc/cpuinfo  |hiển thị thông tin CPU |
cat /proc/meminfo | hiển thị thông tin về RAM đang sử dụng |
cat /proc/version |hiển thị phiên bản của kernel|
cat /etc/redhat-release |hiển thị phiên bản Centos |
uname -a |hiển thị các thông tin về kernel |
free -m |hiển thị lượng RAM còn trống |
df -h |hiển thị thông tin những file hệ thống nơi mỗi file thường trú hoặc tất cả những file mặc định và lệnh này có thể xem được dung lượng ổ cứng đã sử dụng và còn trống bao nhiêu.|
du -sh |xem dung lượng của thư mục hiện tại|
du -ah |xem chi tiết dung lượng của các thư mục con, và cả các file |
du -h --max-depth=1 |xem dung lượng các thư mục con ở cấp 1 (ngay trong thư mục hiện tại) |
df |kiểm tra dung lượng đĩa cứng, các phân vùng đĩa |
top |hiển thị sự hoạt động của các tiến trình, đặc biệt là thông tin về tài nguyên hệ thống và việc sử dụng các tài nguyên đó của từng tiến trình.|
vmstat 3 | kiểm soát hành vi của hệ thống|
vmstat -m |kiểm tra thông tin bộ nhớ |
shutdown, halt|  Tắt hệ thống tại thời điểm yêu cầu |
pwd: |hiển thị thư mục hiện tại|

