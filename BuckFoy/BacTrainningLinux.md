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
             netstat - plnt kiểm tra xem các dịch vụ đang sử dụng cổng mạng nào có thể kết hợp với grep như sau
            netstat -plnt | grep :25 (Kiểm tra xem dịch vụ nào đang sử dụng cổng 25)
            
  # 2.2 PROCESS MONITORING AND SCHEDULING
  
  - Theo dõi các tiến trình
  
          xem thong tin tiến trình : ps -ax 
          xem tiến trình cha con: ps -ef |more
          xem tiến trình theo cấu trúc : pstree -np | more
          xem tiến trình user khởi tạo: ps -u user
          hủy tiến trình: pkill pid
          hủy tiến trình theo tên : pkill name
      
![image4](https://user-images.githubusercontent.com/74639473/99790483-c8254680-2b56-11eb-995a-492e56cc94cc.png)

![image18](https://user-images.githubusercontent.com/74639473/99790518-d5423580-2b56-11eb-92f0-b196609fa393.png)

## Shared Library

  Library là file chứa các đoạn mã lệnh và dữ liệu được tổ chức thành các hàm (subroutine), các lớp (class) nhằm cung cấp dịch vụ, chức năng nào đó cho các chương trình chạy trên máy tính.
  
xác định các library cần thiết cho 1 chương trình.

        # ldd program1, program2

![image23](https://user-images.githubusercontent.com/74639473/99790596-f145d700-2b56-11eb-85d0-8a8b2bca6306.png)

- tạo index cho các  shared library
      
Các library được đặt trong các thư mục như /lib, /usr/lib, /usr/local/lib. Để hướng dẫn cho ld.so tìm kiếm library trong các thư mục này cũng như là các thư mục khác thì có 2 cách:

  1.thêm danh sách các thư mục đó vào biên môi trường shell là LD_LIBRARY_PATH
  
2. tạo index gồm tên các library và thư mục lưu trữ chúng. File /etc/ld.so.cache chứa thông tin index này
  
  thêm các index vào library  
  
        # ldconfig [options] /lib_dirs
        trong đó options gồm -p hoặc -v 
        # ldconfig -v /lib_dirs
        
![image6](https://user-images.githubusercontent.com/74639473/99791012-8b0d8400-2b57-11eb-911a-b3055675add4.png)

   Để xem nội dung của ld.so.cache: # ldconfig -p

![image1](https://user-images.githubusercontent.com/74639473/99791018-8cd74780-2b57-11eb-9bc8-97259a51c1c4.png)

Tìm kiếm một library trong cache: # ldconfig -p | grep ncurses

![image13](https://user-images.githubusercontent.com/74639473/99791065-a4163500-2b57-11eb-9e50-96d9152e6eb8.png)

- Scheduling processes with cron

    Cron là một tiện ích giúp lập lịch chạy những dòng lệnh bên phía server để thực thi một hoặc nhiều công việc nào đó theo thời gian được lập sẵn. Một số người gọi những công việc đó là Cron job hoặc Cron task

## 2.3 Cấu hình SSH
 ### Cấu hình cơ bản
  - kiểm tra đã cấu hình SSH chưa: service sshd restart
  
  - cài đặt ssh: yum install openssh-server -y
  
  - cài đặt mobaXterm
  
  - thực hiện ssh trên mobaXterm
  
  ![image19](https://user-images.githubusercontent.com/74639473/99811870-4f36e680-2b78-11eb-813f-aee1e4b58828.png)

![image26](https://user-images.githubusercontent.com/74639473/99811895-56f68b00-2b78-11eb-94bc-7611d15ca828.png)

  - câu lệnh thực hiện kiểm tra ssh
  
        /etc/ssh/sshd_config : Cấu hình OpenSSH Server
        /etc/ssh/ssh_config : Cấu hình OpenSSH Client
        ~/.ssh/ : Thực mục SSH user
        ~/.ssh/authorized_keys hoặc ~/.ssh/authorized_keys : Thư mục chứa public key (RSA hoặc DSA) dùng để cấu hình SSH auth
        /etc/nologin : Nếu file này tồn tại , SSH sẽ từ chối mọi user trừ root
        /etc/host.allow và /etc/hosts.deny : Thư mục Access List của SSH
      
   ### Cấu hình SSH nâng cao
   
   - Đổi cổng ssh 22 thành cổng khác lớn hơn 1024
    
   ![image14](https://user-images.githubusercontent.com/74639473/99812363-f61b8280-2b78-11eb-9757-e68470dadbe4.png)
 
   sau đó mở port trên iptable chuyển 22 thành 2222
   
   service sshd restart
    
   service iptable restart
   
  - giới hạn số lần đăng nhập sai và phiên làm việc
  
 ![image7](https://user-images.githubusercontent.com/74639473/99812381-f9af0980-2b78-11eb-946c-3ec4fdc3f4e4.png)

  - sử dụng ssh Protocol 2 
  
![image8](https://user-images.githubusercontent.com/74639473/99812386-fae03680-2b78-11eb-9469-22f1cb86ccf3.png)

  - logout ssh nếu phiên đang nhàn rỗi

![image21](https://user-images.githubusercontent.com/74639473/99812397-ffa4ea80-2b78-11eb-8279-772967f8ede9.png)
    
## 2.4. BACKUP AND RESTORE

### Archiving with tar  
- tạo file nén tar

     tar -cvf file.tar ./file
   
 - tạo file nén tar.gz
 
      tar -cvzf file.tar.gz ./file
    
- tạo file nén tar.bz2

	tar -cvjf file.tar.bz2 ./file
      
      trong đó c là tạo file .tar mới
        v: hiển thị quá trình nén file
        f: tên file
        z: chỉ file gzip để nén
        j để  chỉ nén bz2
        
- giải nén toàn bộ file

      giải nén file tar
      tar -xvf file.tar
      tar -xvf file.tar -C /home/…
      giải nén file tar.gz
      tar -xvf file.tar.gz
      tar -xvf file.tar.gz -C /home/…

 liệt kê các file nén
 
     tar -tvf file.tar	
 
giải nén 1 file trong file nén

     tar -xvf file.tar file1

giải nén nhiều file

    tar -xvf file.tar file1 file2

 giải nén nhiều file cùng dạng

    tar -xvf file.tar --wildcards ‘*.jpg’

thêm file vào file nén

    tar -rvf file.tar file1

kiểm tra một file .tar

    tar -tvf file.tar

kiểm tra kích thước file nén

    tar -czf - sampleArchive.tar | wc -c
    
  ### Using the dd command
  
 - Câu lệnh dd dùng để sử dụng trong các trường hợp sau:
 
    -  Sao lưu và phục hồi toàn bộ dữ liệu ổ cứng hoặc một partition
    - Chuyển đổi định dạng dữ liệu từ ASCII sang EBCDIC hoặc ngược lại
    - Sao lưu lại MBR trong máy (MBR là một file dữ liệu rất quan trọng nó chứa các lệnh để LILO hoặc GRUB nạp hệ điều hành)
    - Chuyển đổi chữ thường sang chữ hoa và ngược lại
    -  Tạo một file với kích cỡ cố định
    - Tạo một file ISO
-  cú pháp sử dụng dd

      dd if=<địa chỉ đầu vào> of=<địa chỉ đầu ra> option
    
- sao chép  và phục hồi ổ cứng hoặc phân vùng ổ cứng

    sao lưu ổ cứng này sang ổ cứng khác
    
        dd if=/dev/sdb of=/dev/sdc conv=noerror, sync
        
    tạo 1 file image cho sdb1
    
      dd if=/dev/sdb1 of=/root/sdb1.img 
      
    nén ảnh file vào
    
      dd if=/dev/sdb1 | grip > /root/sdb1.img.gz
    sao lưu phân vùng này sang   phân vùng khác
    
      dd if=/dev/sdb2 of=/dev/sdc2 bs=512 conv=noerror,sync
    phục hồi dữ liệu
      
      dd if=/root/sda1.img of=/dev/sda1
    phục hồi từ CDrom
        
        dd if=/dev/cdrom of=/root/cd rom.img conv=noerror
#### sao lưu và phục hồi MBR
   Sao chép MBR
      
      dd if=/dev/sda1 of=/root/mbr.txt bs=512 count=1
phục hồi MBR
      
      dd if=/root/mbr.txt of=/dev/sda1
tạo một file có lưu lượng cố định
    
    dd if=/dev/zero of=/root/file1 bs=100M count=1
    
## Mirroring data between systems: rsync
 Các đặc điểm nổi bật khi dùng Rsync

      Hiệu quả trong việc sao lưu và đồng bộ file từ 1 hệ thống khác
      Hỗ trợ sao chép links, devices, owners, groups and permissions.
      Nhanh hơn sử dụng SCP (secure copy).
      Rsync tiêu tốn ít bandwidth vì nó có sử dụng cơ chế nén khi truyền tải và nhận dữ liệu.
      
cú pháp :
    rsync options source destination
    
    Các tùy chọn trong rsync
    v : verbose
    r : sao chép dữ liệu theo cách đệ quy ( không bảo tồn mốc thời gian và permission trong quá trình truyền dữ liệu)
    a :chế độ lưu trữ cho phép sao chép các tệp đệ quy và giữ các liên kết, quyền sở hữu, nhóm và mốc thời gian
    z : nén dữ liệu
    h : định dạng so
  
## Cấu hình SSH bằng Key
- tạo khóa trên Server
 câu lệnh : ssh-keygen -t rsa
 
 ![hinh1](https://user-images.githubusercontent.com/74639473/99883096-9ba52380-2c57-11eb-9810-8894a56a595f.png)
 
 - thực hiện cài đặt trên client
 
    trên MobaXterm, click vào tab Tools
    
    Trong các sub tab của Tools, kích chọn MobaKeyGen
    
    ![hinh2](https://user-images.githubusercontent.com/74639473/99883165-33a30d00-2c58-11eb-8cb2-bba860b1ae2d.png)
    
    ![hinh3](https://user-images.githubusercontent.com/74639473/99883168-3867c100-2c58-11eb-9f80-7e13ea0be56b.png)

Chọn đường dẫn đến file copy key từ server đã lưu ở phần trên. Sau đó sẽ có 1 của sổ hiện ra như bên dưới, nhập vào mật khẩu Passphrase sau đó chọn Ok để tiếp tục.

![hinf4](https://user-images.githubusercontent.com/74639473/99883171-3b62b180-2c58-11eb-854b-820f2ec2f8db.png)

![hinh5](https://user-images.githubusercontent.com/74639473/99883172-3d2c7500-2c58-11eb-8c45-5d9c3caf226e.png)

![hinh6](https://user-images.githubusercontent.com/74639473/99883174-3e5da200-2c58-11eb-96cc-5c132de015aa.png)



