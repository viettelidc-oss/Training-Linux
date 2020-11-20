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
câu lệnh | ý nghĩa |
|----------| --------|
cat /proc/cpuinfo  | thông tin về hệ thống |
cat /proc/cpuinfo  | hiển thị thông tin CPU |
cat /proc/meminfo | hiển thị thông tin về RAM đang sử dụng |
cat /proc/version | hiển thị phiên bản của kernel|
cat /etc/redhat-release | hiển thị phiên bản Centos |
uname -a | hiển thị các thông tin về kernel |
free -m | hiển thị lượng RAM còn trống |
df -h | hiển thị thông tin những file hệ thống nơi mỗi file thường trú hoặc tất cả những file mặc định và lệnh này có thể xem được dung lượng ổ cứng đã sử dụng và còn trống bao nhiêu.|
du -sh | xem dung lượng của thư mục hiện tại|
du -ah | xem chi tiết dung lượng của các thư mục con, và cả các file |
du -h --max-depth=1 | xem dung lượng các thư mục con ở cấp 1 (ngay trong thư mục hiện tại) |
df | kiểm tra dung lượng đĩa cứng, các phân vùng đĩa |
top | hiển thị sự hoạt động của các tiến trình, đặc biệt là thông tin về tài nguyên hệ thống và việc sử dụng các tài nguyên đó của từng tiến trình.|
vmstat 3 | kiểm soát hành vi của hệ thống |
vmstat -m | kiểm tra thông tin bộ nhớ |
shutdown, halt |  Tắt hệ thống tại thời điểm yêu cầu |
pwd: | hiển thị thư mục hiện tại |

### add disk và cấu hình LVM
  - cấu trúc LVM 
  
  ![image15](https://user-images.githubusercontent.com/74639473/99787412-47fce200-2b52-11eb-83a5-7a2d52f3daa1.png)
- cài đặt LVM:

  add đĩa vào máy  ảo Centos
  
  ![image9](https://user-images.githubusercontent.com/74639473/99787752-b8a3fe80-2b52-11eb-82a4-704255c4841a.png)
  
  xem máy ảo đã nhận disk chưa : lsblk
  
![image2](https://user-images.githubusercontent.com/74639473/99787848-dcffdb00-2b52-11eb-9f3f-7afdb7b69193.png)

  Tạo các partition cho các ổ mới , bắt đầu từ sdb với lệnh :fdisk /dev/sdb
  
  ![image11](https://user-images.githubusercontent.com/74639473/99788055-23553a00-2b53-11eb-8cd5-c62c0f4fad60.png)
  
  Tạo Physical Volume :pvcreate /dev/sdb1
    tạo Volume Group :vgcreate vg-demo1 /dev/sdb1 /dev/sdc1
    tạo logical Volume: lvcreate -L 1G -n lv-demo1 vg-demo1
    
![image5](https://user-images.githubusercontent.com/74639473/99788406-a4accc80-2b53-11eb-8081-4e156dabfdcc.png)

# 1.2. USER ACCOUNT MANAGEMENT
###  Files used in creating a user
- tập tin /etc/passwd:  là csdl các tài khoản người dùng trên Linux dưới dạng văn bản: xem thông tin file /etc/passwd : cat /etc/passwd
- tập tin /etc/shadow: là nơi lưu trữ mật khẩu đã được mã hóa.xem thông tin file /etc/shadow là cat /etc/shadow
- tập tin /etc/group là nơi lưu thông tin các nhóm
- tạo người dùng và mật khẩu:

![image24](https://user-images.githubusercontent.com/74639473/99788771-2e5c9a00-2b54-11eb-94c9-7b67fb2678ad.png)

   tạo Group: groupadd  group
      
   ![image17](https://user-images.githubusercontent.com/74639473/99788819-403e3d00-2b54-11eb-92c6-654714aa86a1.png)
      
   add user vào group : usermod - g group user

![image25](https://user-images.githubusercontent.com/74639473/99788907-5f3ccf00-2b54-11eb-8b3e-38fa78397f0b.png)

  chmod và chown 
  
  ![image16](https://user-images.githubusercontent.com/74639473/99788979-78458000-2b54-11eb-8d6c-cfc9a1922b8f.png)
  
 ## Lenh sudo 
 
  Cho phép một số user được định nghĩa trong file cấu hình /etc/sudoers có thể chạy một số câu lệnh xác định với quyền hạn root hoặc với quyền hạn của một user khác. khi sử dụng sudo thì yêu cầu nhập password trước khi thực hiện. các lệnh sudo sẽ ghi log lại trong file var/log/messages
  
 #1.3. PACKAGE MANAGEMENT
 
###  Xem cu phap lenh rpm: man rpm
  - cài đặt gói: rpm -ivh <tap tin.rpm>
- thông tin  tập tin của 1 gói: rpm -qpl <tập tin rpm>
- xóa một gói :rpm -e <tên gói>
- thông tin về gói : rpm -qpi <tên gói>
- liệt kê ds các gói đã cài đặt : rpm -qa
- kiểm tra gói : rpm -q
- liên kết ds tập tin của 1 gói: rpm -ql <tên gói>
- cho biết tập tin thuộc gói nào : rpm -qf <tập tin>
  
  ### Yum 
 - xem thông tin gói tin : yum info <gói tin> 
 - update gói tin: yum update
 - liệt kê các gói tin đã đặt : yum list install
 
 
 
 #   REPORT
 
 #   2.1.   SYSTEM STARUP AND SHUTDOW
 ## Quá trình khởi tạo hệ thống
 
Linux boot process là quá trình khởi tạo hệ thống Linux. Nó bao bước từ khi ta bật máy đến khi giao diện người dùng sẵn sàng.
Khi bật máy tính, quá trình khởi động sẽ được thực hiện theo các bước dưới đây:BIOS thực hiện kiểm tra tính toàn vẹn trên bộ nhớ và tìm kiếm các chỉ dẫn trên MBR.MBR nạp trình quản lý khởi động LILO hoặc GRUB.LILO/GRUB sẽ nhận diện kernel hệ điều hành và nạp nhân hệ điều hành từ /boot.Nhân hệ điều hành chuyển quyền điều khiển cho chương trình /sbin/init.Dựa trên mức hoạt động tương ứng, /sbin/init thực hiện nạp các dịch vụ và gắn kết (mount) tất cả các phần chia của hệ thống (trong /etc/fstab)
- tiến trình init: các bước khởi tạo

            Đầu tiên, init gọi thi hành /etc/rc.d/rc.sysinit để thiết lập đường dẫn, kiểm tra các hệ thống tập tin v.v…
            
            Kế tiếp, init sẽ thực thi /etc/inittab (mô tả các mức thi hành).
            
            Init gọi thi hành script /etc/rc.d/init.d/functions cho biết các khởi động hay ngừng một chương trình và cách xác định PID của một chương trình.
            
            Tiếp tục, init khởi động các tiến trình ngầm nằm trong các thư mục /etc/rc.d/rc0.d/, /etc/rc.d/rc1.d/…
            
            Gọi thi hành /etc/rc.d/rc.local: bổ sung thêm các lệnh cần thiết.
            
            Sau khi init đã xử lý tất cả các mức thi hành, script /etc/inittab phát sinh một tiến trình getty cho mỗi virtual console cho mỗi mức thi hành.
    - khung tập lệnh 
    
            startx khởi động chế độ xwindows từ cửa sổ terminal
  -   quản lý các service
  
            service --status-all Kiểm tra tất cả các service và tình trạng của nó.
            service mysql stop shutdown dịch vụ mysql.
            service httpd start khởi động dịch vụ httpd.
            whereis mysql hiển thị nơi các file dịch vụ được cài đặt.
             service --status-all | grep abc, xem tình trạng của tiến trình abc
            service start | stop | restart
            /etc/init.d/ start | stop

  



      



