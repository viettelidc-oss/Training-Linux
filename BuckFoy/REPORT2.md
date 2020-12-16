# Report 2

<a name="mucluc"> </a>
#  muc lục

[1.  SYSTEM STARTUP AND SHUTDOWN](#P1)

 * [1.1. System startup process ](#P11)

* [1.2. The startup script framework](#P12)

* [1.3. Managing services using](#P13)

* [1.5. Managing services with system/service/systemctl](#P15)

* [1.6 Shutdowns and rc](#P16)

[2.  PROCESS MONITORING AND SCHEDULING](#P2)

* [2.1 Monitoring processes](#P21)

* [2.2 Shared Libraries](#P22)

* [2.3 Scheduling processes with cron](#P23)

* [2.4 Crontab command options](#P24)

[3.  SYSTEM SECURITY AND ENCRYPTION](#P3)

*  [3.1 The secure shell OpenSSH](#P31)

* [3.2. Public / Private key authentication](#P32)

* [3.3. X11 Forwarding](#P33)

[4.  BACKUP AND RESTORE](#P4)

* [4.1. Archiving with tar](#P41)

* [4.2. Using the dd command](#P42)

* [4.3. Mirroring data between systems: rsync](#P43)

# 1. SYSTEM STARTUP AND SHUTDOWN
<a name="P11"> </a>
## 1.1.	System startup process
### 1.1.1.	Linux boot process là gì ?
Linux boot process là quá trình khởi tạo hệ thống Linux. Nó bao gồm từ khi ta bật máy đến khi giao diện người dùng sẵn sàng.
### 1.1.2. Quá trình khởi động hệ thống
Gồm có 8 bước: 

Bước 1. BIOS

Bước 2. Master boot Record (MBR)

Bước 3.  Boot loader

Bước 4. Kernel  được nạp và khởi chạy

Bước 5. các script trong INITRD thực thi

Bước 6. Chương trình init được thực thi

Bước 7. Đăng nhập giao diện đồ họa

Bước 8. Đăng nhập thành công vào hệ thống

![hinh11](https://user-images.githubusercontent.com/74639473/100110342-69860280-2e9f-11eb-91aa-468d7f0e9291.png)


#### Bước 1.	BIOS

BIOS là chương trình chạy đầu tiên khi nhấn nút nguồn hoặc reset lại máy tính.

BIOS thực hiện một công việc gọi là POST (power on self test) kiểm tra các thông số phần cứng của máy tính. Ngoài ra, BIOS cho phép thay đổi cấu hình, thiết lập nó.
BIOS được lưu trữ trên ROM của bo mạch chủ.

Quá trình POST kết thúc thành công, BIOS sẽ tìm kiếm và   khởi  chạy một hệ điều hành trong các thiết bị lưu trữ như ổ cứng . ..

Hệ điều hành Linux được cài trên ổ cứng thì BIOS sẽ tìm kiếm đến MBR.

#### Bước 2. Master Boot Record
Sau khi BIOS xác định được thiết bị lưu trữ thì BIOS sẽ đọc trong MBR hoặc phân vùng EFI của thiết bị để nạp vào bộ nhớ  một chương trình. Chương trình này sẽ định vị và khởi động boot loader.
	
Đến giai đoạn này, máy tính sẽ không truy cập vào phương tiện lưu trữ. Thông tin về ngày tháng, thời gian và các thiết bị quan trọng nhất được nạp từ CMOS

![hinh12](https://user-images.githubusercontent.com/74639473/100110366-6e4ab680-2e9f-11eb-897c-cfbf8cbafd3b.png)


#### Bước 3. Boot Loader

Linux có 2 boot loader phổ biến trên Linux là GRUB và ISOLINUX.

Mục đích là cho phép lựa chọn hệ điều hành có trên máy tính để khởi động, sau đó chúng sẽ nạp kernel của hệ điều hành vào bộ nhớ và chuyển quyền điều khiển máy tính cho kernel này.

Ví dụ file cấu hình grub.cfg: “/boot/grub/grub.conf ”

![hinh13](https://user-images.githubusercontent.com/74639473/100110375-70147a00-2e9f-11eb-8c81-0b3d7367f3f7.png)

Hệ thống sử dụng BIOS/MBR , bộ tải khởi động nằm ở khu vực đầu tiên của đĩa cứng. Kích thước của MBR chỉ là 512 byte. Bộ nạp khởi  động kiểm tra bảng phân vùng và tìm một phân vùng có khả năng khởi động. Nó tìm thấy 1 phân vùng có khả năng khởi động, nó sẽ tìm kiếm bộ tải khởi động giai đoạn thứ 2.

Với hệ thống sử dụng EFI / UEFI, phần mềm UEFI đọc dữ liệu trình quản lý khởi động để xác định ứng dụng UEFI nào sẽ được khởi chạy và từ nơi nào.

Trình khởi động giai đoạn 2 nằm trong /boot. Hiển thị cho chúng ta chọn hệ điều hành để khởi động. Tiếp đến bộ nạp khởi động sẽ tải hệ điều hành vào RAM và chuyển quyền kiểm soát cho RAM.


#### Bước 4. Linux kernel được nạp và khởi chạy

Boot loader nạp một phiên bản dạng nén của Linux kernel. Nó tự giải nén và tự cài đặt lên bộ nhớ hệ thống nơi mà nó sẽ ở đó cho tới khi tắt máy.

![hinh 14](https://user-images.githubusercontent.com/74639473/100110399-773b8800-2e9f-11eb-8dee-7721946ac4bd.png)

Sau khi chọn kernel trong file cấu hình của Boot loader , hệ thống sẽ tự động nạp chương trình init trong thư mục /sbin

![hinh 15](https://user-images.githubusercontent.com/74639473/100110420-7d316900-2e9f-11eb-8ca0-6deaf2ea8a87.png)

#### Bước  5. Các Script trong INITRD thực thi

INITRD cung cấp một giải pháp: là một tập các chương trình sẽ được thực thi khi kernel vừa mới được khởi chạy. Các chương trình này sẽ dò quét phần cứng của hệ thống và xác định xem kernel cần được hỗ trợ thêm những gì để có thể quản lý được các phần cứng đó. Chương trình INITRD có thể nạp thêm vào kernel các module bổ trợ. Khi chương trình INITRD kết thúc thì quá trình khởi động Linux sẽ tiếp diễn.

Hệ thống hình ảnh tập tin initramfs chứa các chương trình và tệp nhị phân thực hiện các hành động cần thiết để gắn kết hệ thống tệp gốc thích hợp, cung cấp chức năng hạt nhân cho hệ thống tệp và trình điều khiển thiết bị cần thiết cho bộ điều khiển lưu trữ hàng loạt với cơ sở được gọi là udev (cho thiết bị người dùng). Thiết bị nào có mặt, định vị các trình điều khiển thiết bị mà chúng cần để hoạt động chính xác và tải chúng. Sau khi hệ thống tập tin gốc đã được tìm thấy, nó được kiểm tra lỗi và được gắn kết.

![hinh16](https://user-images.githubusercontent.com/74639473/100110435-7f93c300-2e9f-11eb-9cf3-ff832ed69642.png)

Chúng ta có thể thấy tệp kernel và initramfs trong thư mục /boot

![hinh 17](https://user-images.githubusercontent.com/74639473/100110447-84587700-2e9f-11eb-9c9d-40470ab6e0b0.png)

####  Bước 6. Chương trình init thực thi
Kernel được khởi chạy xong, nó sẽ gọi duy nhất 1 chương trình là init.

tiến trình có ID =1 init là cha của tất cả các tiến trình khác mà có trên hệ thống Linux.

Init xử lý việc gắn và xoay vòng vào hệ thống tập tin gốc thực sự cuối cùng

Có 2 loại Init phổ biến trong Linux:

	dựa trên Unix System V
	dựa trên Systemd
	
- Unix System V

	File cấu hình runlevel /etc/inittab:
	runlevel 0: level shutdown hệ thống
	runlevel 1: level chỉ cho 1 người dùng để sửa lỗi hệ thống tập tin
	runlevel 2: không sử dụng
	runlevel 3: Level dùng cho nhiều người nhưng chỉ giao tiếp dạng text
	runlevel 4: không sử dụng
	runlevel 5: dùng cho nhiều người dùng và cung cấp giao diện đồ họa
	runlevel 6: Reboot hệ thống
	
Chương trình /sbin/init sẽ thực thi các file startup scripts được được trong thư mực con của thư mục /etc/rc.d

- Systemd

	Đối với các bản Linux hiện đại như CentOS 7, RHEL 7 gần đây thì init và runlevel được thay thế bởi systemd và cũng thực hiện nhiệm vụ tương ứng. Systemd cũng giống như init là tiến trình chạy đầu tiên trên hệ thống với ID = 1.
	Systemd đọc tệp liên kết bởi /etc/systemd/system/default.target để xác định đích hệ thống mặc định.
	Tệp mục tiêu trong hệ thống xác định các dịch vụ mà systemd bắt đầu.
	Systemd sử dụng mục tiêu thay vì runlevel. Systemd có hai mục tiêu chính: multi-user.target và graphical.target tương ứng với runlevel 3 và runlevel 5
	
#### Bước 7. Đăng nhập với giao diện đồ họa

Gần cuối quá trình khởi động, init sẽ bắt đầu một chế độ đăng nhập text mode. Nhập tên người dùng và mật khẩu của bạn để đăng nhập và xuất hiện các dấu nhắc lệnh shell.


![hinh 18](https://user-images.githubusercontent.com/74639473/100110457-87536780-2e9f-11eb-8541-1dd8f2654a76.png)

Subsystem cuối cùng được init khởi động lên là X Window, là một hệ thống giao diện đồ họa người dùng của Linux.

Cách truy cập các terminal qua phím ALT

- Các terminal chạy các lệnh sell có thể được truy cập bằng ATL + một phím chức năng.

- Trong môi trường đồ họa, việc chuyển sang bàn điều khiển văn bản yêu cầu nhấn CTRL-ALT + phím chức năng thích hợp (với F7 hoặc F1 dẫn đến GUI)

#### Bước 8 Đăng nhập thành công vào hệ thống

Shell lệnh mặc định là bash (GNU Bourne Again Shell), nhưng có một số shell lệnh nâng cao khác có sẵn. Nó đã sẵn sàng chấp nhận các lệnh, bạn gõ lệnh và nhấn Enter ,lệnh được thực hiện.
### 1.1.3. Tài liệu tham khảo

[1] https://blogd.net/linux/qua-trinh-khoi-dong-he-dieu-hanh-linux/

[2]https://kipalog.com/posts/Tim-hieu-ve-Linux-boot-process----2

[3] https://manthang.wordpress.com/2011/01/04/tim-hieu-qua-trinh-khoi-dong-cua-may-linux/

[trở về mục lục](#mucluc)


<a name="P12"> </a>
## 1.2. The startup script framework

Chương trình /sbin/init sẽ thực thi các file startup script được đặt trong các thư mục con của thư mục /etc/rc.d.

![hinh 19](https://user-images.githubusercontent.com/74639473/100110465-8b7f8500-2e9f-11eb-9a1c-78f7bf841666.png)

Script sử dụng run level từ 0 đến 6 để xác định thư mục chứa file script chỉ định cho từng level như: /etc/rc.d/rc0.d đến /etc/rc.d/rc6.d

Trong file script theo từng level. Các tên tập tin bắt đầu bằng từ khóa "S" có nghĩa là tập tin này sẽ được thực thi lúc khởi động hệ thống. Nếu tập tin bắt đầu bằng từ khóa "K" nghĩa là tập tin đó được thực thi khi hệ thống shutdown. Số theo sau từ khóa "S" và "K" để chỉ định trình tự khởi động các script, kế tiếp là tên file script cho từng dịch vụ.

Ví dụ 1 số file script theo từng run level:

![hinh 110](https://user-images.githubusercontent.com/74639473/100110470-8d494880-2e9f-11eb-8d2c-ee772476b64f.png)

Các máy tính cho phép tạo lệnh khởi động của riêng mình để thực hiện các tác vụ tự động mỗi khi phiên bản khởi động. Lệnh tập khởi động có thể thực hiện các hành động như cài đặt phần mềm, thực hiện cập nhập, bất dịch vụ hoặc bất kỳ thao tác nào khác được xác định.

VD: tập lệnh khởi động cài đặt và khởi động máy chủ Apache có thể như sau

	#! /bin/bash
	yum update
	yum -y install apache2
	cat <<EOF > /var/www/html/index.html
	<html><body><h1>Hello World</h1>
	<p>This page was created from a startup script.</p>
	</body></html>
	EOF

[trở về mục lục](#mucluc)

<a name="P13" > </a>

## 1.3. Managing services using

### 1.3.1. Service là gì

Service là các chương trình hoặc quy trình luôn chạy trên máy chủ, thường là từ khi máy chủ khởi động. Chúng được sử dụng để cung cấp hỗ trợ liên tục cho các yêu cầu và giám sát, từ các quy trình khác hoặc khách hàng bên ngoài. 

Được gọi với tên là daemons

thường được kết thúc bằng ký tự d. ví dụ: httpd,sshd,named, ftpd,...

được khởi tạo tự động bởi tiến trình init- chương trình đầu tiên được thự hiện sau kernel được nạp

### 1.3.2. Các mô hình quản lý dịch vụ

Mỗi dịch vụ có một tập tin script /etc/init.d để tương tác với dịch vụ. Init sẽ khởi tạo các dịch vụ thông qua các tập tin scripts trong /etc/init.d

 /etc/init.d/script-file{stop/start/restart}
 
Có nhiều chương trình init khác nhau tùy thuộc vào sự lựa chọn của distributor.

thư mục 

	Ví dụ khởi tạo, khởi tạo lại, và kết thúc dịch vụ quản trị mạng
	-sudo /etc/init.d/NetworkManager start
	- /etc/init.d/NetworkManager restart
	- /etc/init.d/NetworkManager stop

![hinh 111](https://user-images.githubusercontent.com/74639473/100113303-c2a36580-2ea2-11eb-8bdf-88425e457ad9.png)

### 1.3.3.	Managing service with update-rc.d

Sử dụng update-rc.d là giúp nó sẽ tự động thực hiện các việc thêm/ gỡ bỏ các symbolic link cần thiết trong /etc/init.d

![hinh 112](https://user-images.githubusercontent.com/74639473/100113313-c46d2900-2ea2-11eb-8c94-2cd70f0967ca.png)

Các runlevel 0,1,6 được bắt đầu bởi chữ K(Kill), các runlevel 2,3,4,5 được bắt đầu bằng chữ S(Start).

Xóa một service : Nếu muốn vô hiệu hóa một Service khi khởi động, có thể thực hiện bằng tay bằng cách xóa hết symbolic links liên quan đến service trong thư mục /etc/rcX.d/ hoặc là sử dụng dụng update-rc.d với câu lệnh

update-rc.d -f <service name> remove
	
với -f là yêu cầu update-rc.d xóa hết symbolic links của service đó

 Thêm một service: muốn thêm một service chạy khi khởi động máy. thực hiện bằng câu lệnh sau: sudo update-rc.d <service name> defaults

[trở về mục lục] (#mucluc)

<a name=P15">  </a>

# 1.5. Managing services with system/service/systemctl
## 1.5.1 Managing service with System V init

Liệt kê tất cả các service đang chạy trên hệ thống SysV init: service  --status-al

![hinh 113](https://user-images.githubusercontent.com/74639473/100113323-c6cf8300-2ea2-11eb-8131-4bc692ef7e72.png)

Lệnh trên liệt kê tất cả các service đang chạy trong hệ thống. Trong trường hợp các service đang chạy rất nhiều, bạn có thể sử dụng các tham số bổ sung - more và less để liệt kê các service trong chế độ xem một cách có tổ chức và rõ ràng

sử dụng lệnh service  --status-al | less

![hinh 114](https://user-images.githubusercontent.com/74639473/100113343-cc2ccd80-2ea2-11eb-80f0-f0d3a11cb5d1.png)

Để chỉ liệt kê các service hiện đang chạy trên hệ thống, hãy thực thi lệnh bên dưới: service --status-al | grep running

![hinh 115](https://user-images.githubusercontent.com/74639473/100113352-cfc05480-2ea2-11eb-8048-e9de4fa03d11.png)

Để xem trạng thái của một service cụ thể, hãy thực thi lệnh bên dưới:

service --status-al | grep <service name>

hoặc 

service <service name> status

![hinh 116](https://user-images.githubusercontent.com/74639473/100113369-d2bb4500-2ea2-11eb-927d-eedd7679ed04.png)

Để liệt kê tất cả các Service được kích hoặc trong boot

![hinh 117](https://user-images.githubusercontent.com/74639473/100113377-d51d9f00-2ea2-11eb-919d-c58b7d8559bf.png)

### 1.5.2 kiểm tra service trong hệ thống systemd init

Để liệt kê tất cả các service trên máy Linux đang chạy hệ thống Systemd init, hãy thực thi lệnh dưới đây: systemctl

Bạn cũng có thể liệt kê các service đang chạy dựa trên loại của chúng bằng lệnh sau :

	systemctl list-units --type service
	
Bạn cũng có thể liệt kê các service dựa trên trạng thái hiện tại của chúng. Kết quả tương đối giống với đầu ra của lệnh trước nhưng đơn giản hơn một chút.
 	
	systemctl list-unit-files --type service

Để liệt kê trạng thái của một service cụ thể, hãy thực thi lệnh bên dưới:
	
	systemctl status [service_name]
	 e.g
	 systemctl status acpid.path
Để chỉ liệt kê các service hiện đang chạy trên hệ thống, hãy thực thi lệnh bên dưới:
	
	systemctl | grep running
Để liệt kê tất cả các service được kích hoạt trong khi boot, hãy thực thi lệnh bên dưới:
	
	systemctl list-unit-files | grep enabled

Bạn cũng có thể xem các control group (nhóm điều khiển) hàng đầu và việc sử dụng tài nguyên hệ thống của chúng như I/O, CPU, Tasks và Memory bằng lệnh systemd-cgtop.
systemd-cgtop

Cũng có thể sử dụng pstree để liệt kê tất cả các service đang chạy trong hệ thống. Pstree lấy thông tin này từ đầu ra hệ thống Systemd. 
	pstree

[trở về mục lục](#mucluc)

<a name="P16"> </a>

# 1.6. Shutdowns and rc

## 1.6.1. lệnh Shutdown 

lệnh shutdown linux có thể dùng để tắt, khởi động lại, và tạm dừng hệ thống. Cú pháp của lệnh shutdown linux như sau:
	
	Shutdown [options] [time] [wall]

trong đó [option] có thể là mốc thời gian. sau đó gõ thêm thông báo để báo máy sắp tắt

định dạng thời gian là hh:mm (giờ: phút) theo dạng 24h. Nó sẽ xác định thời gian chính xác để thực thi lệnh shutdown. Ngoài ra bạn có thể dùng +m với m là số phút, là thời gian chờ để shutdown.

Có thể sử dụng chữ  now hay là +0 để tắt máy ngay. Nếu không đặt tham số [time] , mặc định Linux sẽ hiểu là chờ 1 phút trước khi tắt.

Tham số time là bắt buộc khi nếu muốn hiển thị thông báo. File /run/nologin sẽ được tạo  5 phút trước khi hệ thống tắt  , nếu  đã nhập tham số này, nó cản trở không  cho đăng nhập nữa.

- sử dụng lệnh shutdown trong centos

Lệnh shutdown cơ bản cho cả CentOS như sau: shutdown

Nó sẽ không tắt máy ngay mà sẽ chờ 1 phút.

Để tắt  máy ngay lập tức bạn gõ lệnh sau
		
		shutdown now
Để tắt máy tại thời điểm nhất định, gõ lệnh sau  và nhấn enter

	shutdown hh:mm
để thay thế sau vài phút hoặc vài giờ , gõ lệnh sau:

	shutdown +m
Để tạo thông báo cho cho những ai đang đăng nhập, cú pháp:
	
	Shutdown +m “thông báo tắt máy”

Để khởi động máy : shutdown -r

Lệnh này nó sẽ chờ sau 1 phút trước khi  khởi động lại để nếu có quên lưu dữ liệu.

Để khởi động ngay lập tức, hãy thêm option now
	
	shutdown -r now
Để hẹn giờ khởi động lại máy:
	
	showdown -r hh:mm
Để khởi động sau một khoảng thời gian nhất định:
	
	showdown -r +m
- một số tùy cho cho lệnh shutdown linux 
		
		-poweroff, -P : ngắt điện hệ thống
		-reboot, -r : khởi động lại hệ thống
		-halt, -h: tạm hoãn sau khi thực thi lệnh tắt máy

- Để hủy hẹn giờ trong shutdown: 		shutdown -c
## 1.6.2. rc

## 1.6.3. Tài liệu tham khảo

[1] Cách dùng lệnh shutdown linux trong máy Ubuntu 18.04 và CentOS 7

[trở về muc luc](#mucluc)





<a name="P2"> </a>

# 2. PROCESS MONITORING AND SCHEDULING
<a name="P21"> </a>
## 2.1.	Monitoring processes

### 2.1.1. tiến trình là gì ?

Tiến trình là được hiểu đơn giản là một chương trình đang chạy trong hệ điều hành. Một tiến trình có thể phân thành một hay nhiều tiến trình con khác.

### 2.1.2. Phân loại tiến trình

- Init process

Init process là tiến trình đầu tiên được khởi động sau khi bạn lựa chọn hệ điều hành trong boot loader. Trong cây tiến trình, init process là tiến trình cha của các tiến trình khác. Init process có các đặc điểm sau:

	PID =1
	không thể kill init process
	
- Parents process - Child process

Trong hệ điều hành  linux các tiến trình được phân thành parents process và child process.Một tiến trình khi thực hiện lệnh fork() để tạo ra một tiến trình mới thì được gọi là parents process. Tiến trình mới tạo được gọi là child process.

![hinh 21](https://user-images.githubusercontent.com/74639473/100116765-b4efdf00-2ea6-11eb-92de-851a7af4f6f7.png)

Một parents process có thể có nhiều child process nhưng một child process chỉ có một parents process. Khi quan sát thông tin của một tiến trình, ngoài PID (Process ID) ta cần để ý tới PPID (Parent Process ID). Nó sẽ cho ta thông tin về parents process của tiến trình đó: ps -ef

![hinh 22](https://user-images.githubusercontent.com/74639473/100116793-bfaa7400-2ea6-11eb-8bd5-c4e6765eb8e3.png)

- Orphan process- Zombie Process

Khi parents process – child process hoạt động sẽ xảy ra một số trường hợp đặc biệt. Lúc đó Orphan process – Zombie Process sẽ được hình thành.

Khi một parents process bị tắt trước khi child process được tắt, tiến trình con đó sẽ trở thành một orphan process. Lúc này init process sẽ trở thành cha của orphan processes và thực hiện tắt chúng.

Khi một child process được kết thúc, mọi trạng thái của child process sẽ được thông báo bởi lời gọi hàm wait() của parents process. Vì vậy, kernel sẽ đợi parents process trả về hàm wait() trước khi tắt child process. Tuy nhiên vì một vài lí do mà parents process không thể trả về hàm wait(), khi đó child process sẽ trở thành một zombie process. Khi ở trạng thái này, tiến trình sẽ gần như giải phóng bộ nhớ hoàn toàn, chỉ lưu giữ một số thông tin như PID, lượng tại nguyên sử dụng,… trên bảng danh sách tiến trình.

Tuy giải phóng bộ nhớ hoàn toàn nhưng các zombie process không bị kết thúc. Vì vậy nếu lượng zombie process lớn sẽ nắm giữ lượng lớn các PID. Nếu lượng PID đầy, sẽ không có tiến trình mới được tạo thêm. Các zombie process sẽ chỉ bị kết thúc nếu như parents process của chúng bị kill.

Để tìm các zombie process ta gõ kiểm tra trạng thái của tiến trình theo lệnh sau: ps  -lA | grep ‘^. Z’

![hinh 23](https://user-images.githubusercontent.com/74639473/100116808-c46f2800-2ea6-11eb-9aa7-ab18708c2dd5.png)

- Daemon Process

Một Daemon Process là một tiến trình chạy nền. Nó sẽ luôn trong trạng thái hoạt động và sẽ được kích hoạt bởi một điều kiện hoặc câu lệnh nào đó. Trong Unix, các daemon thường được kết thúc bằng “d” ví dụ như httpd, sshd, crond, mysqld,…

Chúng ta có thể chạy một đoạn script bash shell, python, java,… dưới dạng một daemon process bằng cách sử dụng dấu & ví  dụ:  ./simpleshell.sh &

Tuy nhiên, vấn đề ở đây là khi ta kết thúc phiên của terminal, tiến trình đó sẽ không có tiến trình cha và sẽ trở thành một orphan process. Để giải quyết vấn đề này, ta sẽ cho shell chạy với tư cách là tiến trình con của init process bằng cách dùng lệnh nohup như sau: nohup ./simpleshell.sh &

### 2.1.3. Các tiến trình đang hoạt động

Khi một hệ thống đã vận hành, có rất nhiều chương trình đã và đang hoạt động cùng nhau, cùng phối hợp để khiến cho hệ thống có thể giúp người dùng xử lý các công việc. Các bản phân phối Linux cũng giống như các hệ điều hành hiện đại ngày nay, hoạt động theo cơ chế đa nhiệm, tức là trong cùng 1 thời điểm có thể có nhiều chương trình cùng (có vẻ) thực thi tại 1 thời điểm. Tất nhiên thực tế điều này không bao giờ xảy ra, các chương trình đã được phân chia thời gian hoạt động và hệ điều hành điều phối hoạt động tốt đến mức ta không nhận ra được các chương trình thực tế đang chạy tuần tự mà nghĩ rằng nó đang chạy song song.

Ngoài sự đa nhiệm, Linux còn hỗ trợ cơ chế đa người dùng, tức là tại 1 thời điểm, có thể có nhiều chương trình được hoạt động với người dùng là những người khác nhau. Hệ điều hành quản lý tất cả các tiến trình này và vẫn đảm bảo trải nghiệm là đồng đều giữa các người dùng cũng như giữa các chương trình. Một chương trình đặc biệt là top có thể giúp ta biết được hệ thống hiện tại có các chương trình nào đang hoạt động.

![hinh  24](https://user-images.githubusercontent.com/74639473/100116853-cd5ff980-2ea6-11eb-9940-adebd6db1f08.png)

lệnh top có biết khá nhiều thông tin của các tiến trình:

	dòng thứ nhất cho biết thời gian uptime,  số người dụng thực tế hoạt động.
	dòng thứ 2 là thống kê về số lượng tiến trình, bao gồm tổng số tiến trình (total), số đang hoạt động (running), số đang ngủ/chờ(sleeping), số đã dừng(stopped) và số không thể dừng hẳn (zombie)
	dòng thứ 3-5 laf lần lượt thông tin về CPU, RAM, và bộ nhớ Swap
	các dòng còn lại liệt kê  chi tiết về các tiến trình như định danh (PID), người dùng thực thi (User), độ ưu tiên(PR), dòng lệnh thực thi(Command),...

### 2.1.4. Kết thúc một tiến trình đang hoạt động

Nếu một tiến trình đang bị treo (not responding), nhưng không thể tắt nó thì cần phải sử dụng các công cụ dòng lệnh ps và kill. Lệnh kill được dùng để kết thúc một tiến trình dựa trên định danh các tiến trình PID, và để biết được PID của tiến trình cần buộc kết thúc, có thể dùng ps kết hợp với redirection bằng grep. Ta sử dụng câu lệnh ps aux | grep ‘ten tien trinh bi treo’

![hinh 25](https://user-images.githubusercontent.com/74639473/100116862-cfc25380-2ea6-11eb-8bb7-25c963934a86.png)

Trình duyệt opera  chạy rất nhiều tiến trình, vậy ta thử tắt chứng đi, ta sử dụng lệnh kill -9 PID, thử tắt tiến trình PID = 8768, và opera đã được đóng lại.

Trong trường hợp khi không thể thao tác bằng chuột hoặc hệ thống không cho phép mở một terminal thì ta có thể sử dụng tổ hợp phím Ctrl+Alt+F<Console> với Console là 1 trong các giá trị từ 1-12. Khi ấn tổ hợp phím này, một giao diện dòng lệnh sẽ được kích hoạt (gọi là tty). Ta có thể sử dụng giao diện dòng lệnh này để kill các tiến trình bị treo. Đặc biệt sau khi kill được các tiến trình đó, ta có thể dùng tổ hợp phím Ctrl+Alt+F7 để quay về giao diện đồ họa. Nếu cách này không hiệu quả (nguyên do là vì CPU và RAM đều quá tải) thì ta buộc phải khởi động lại hệ thống.
	
[trở về mục luc](#mucluc)

<a name="P22"> </a>

## 2.2. Shared Libraries

### 2.2.1. Shared Library và Dynamic Linking

Library là file chứa các đoạn mã lệnh và dữ liệu được tổ chức thành các hàm (subroutine), các lớp (class) nhằm cung cấp dịch vụ, chức năng nào đó cho các chương trình chạy trên máy tính.

Library gồm 3 loại: Static, Dynamic và Shared. Thường thì các library ở dạng mã nhị phân, không phải dạng văn bản thuần túy (plain text) – là các ký tự mà con người có thể đọc hiểu được.

Khi biên dịch 1 chương trình đang ở dạng các file source code (gồm tập các câu lệnh, khai báo được viết bằng 1 ngôn ngữ lập trình cấp cao như C/C++, Java…) sang dạng executable (hay binary – tập các mã máy nhị phân mà chỉ có CPU mới hiểu được) thì nhiều hàm chức năng của chương trình được liên kết từ các library. Quá trình biên dịch này do bộ biên dịch (Compiler) đảm nhiệm.

Một chương trình đã được biên dịch, chứa đoạn mã trong các library và được lưu trữ ở bộ nhớ ngoài (HDD, USB…) được coi là được liên kết tĩnh (Static Linking) bởi vì khi chạy nó hoàn toàn độc lập, không còn phụ thuộc vào sự tồn tại của các library chứa đoạn mã đó nữa . “Chương trình được liên kết tĩnh” có 1 số điểm hạn chế như:

– Chương trình sẽ “phình to” hơn về kích thước chiếm dụng trên bộ nhớ ngoài do phải bao gồm đoạn mã của library được liên kết trong chương trình.

– Gây ra sự lãng phí bộ nhớ RAM nếu nhiều chương trình đang chạy đồng thời chứa cùng đoạn mã giống nhau trong library.

![hinh 26](https://user-images.githubusercontent.com/74639473/100116874-d2bd4400-2ea6-11eb-921c-2b1f36b487e3.png)

Để khắc phục 2 nhược điểm trên, nhiều chương trình được liên kết động (Dynamic Linking) tức là:

– Bản thân các chương trình này khi được lưu trữ ở bộ nhớ ngoài không chứa các đoạn mã trong library mà chỉ chứa khai báo tham khảo tới đoạn mã đó. Điều này giúp giảm kích cỡ của chương trình.

– Khác với static linking, việc liên kết tới library diễn ra tại thời điểm biên dịch. Ở dynamic linking, việc liên kết giữa các file thực thi (ở dạng binary) của chương trình với library diễn ra tại thời điểm chạy chương trình (runtime). Quá trình gắn kết này do bộ Linker đảm nhiệm giúp cho phép nhiều chương trình sử dụng chung library trong bộ nhớ.

![hinh 27](https://user-images.githubusercontent.com/74639473/100116883-d51f9e00-2ea6-11eb-98f9-9eec59a7cee6.png)

Các file library được liên kết động và được dùng chung bởi nhiều ứng dụng được gọi là shared library. Trên Windows các file này có phần mở rộng là .dll , còn Linux là các file .so

### 2.2.2 Xác định các  shared library của một tiến trình 

Bất kỳ chương trình nào sử dụng dynamic linking đều yêu cầu 1 vài shared library có trên hệ thống. Nếu các library cần thiết không được tìm thấy (hoặc không tồn tại), khi chạy chương trình sẽ đưa ra thông báo lỗi.

Tiện ích ldd sẽ giúp xác định các  library cần thiết cho một chương trình
 	
	# ldd  program1, program2…
	
Lệnh trên sẽ hiện thi các shared library cho các chương trình program 1 , program 2 … .Kết  quả cho biết tên của library cùng với vị trí bạn cần đặt nó vào cây thư mục.

Ví dụ: ldd /bin/bash

![hinh 28](https://user-images.githubusercontent.com/74639473/100116900-d81a8e80-2ea6-11eb-83f7-b44ab50872dd.png)

### 2.2.3. Tạo chỉ mục tìm kiếm index cho các shared library

Khi các chương trình ở dạng executable có sử dụng dynamic linking được chạy, thì tiện ích ld.so sẽ chịu trách nhiệm tìm kiếm và nạp vào bộ nhớ các shared library cần thiết cho chương trình đó.  Nếu ld.so không thể tìm thấy các library đó thì chương trình sẽ gặp lỗi và không thể chạy được.

Thường thì các library được đặt trong các thư mục như /lib, /usr/lib, /usr/local/lib. Để hướng dẫn cho ld.so tìm kiếm library trong các thư mục này cũng như là các thư mục khác thì có 2 cách:

Đơn giản nhất, bạn thêm danh sách các thư mục đó vào biên môi trường shell là LD_LIBRARY_PATH. Tuy nhiên, cách nay có thể không thích hợp đối với các system library, vì có thể người dùng sẽ chỉnh sửa sai biến LD_LIBRARY_PATH này.

Cách còn lại là tạo index gồm tên các library và thư mục lưu trữ chúng. File /etc/ld.so.cache chứa thông tin index này. Đây là file nhị phân, vì thế ld.so có thể nhanh chóng đọc nội dung của file này.

Để thêm mới index của library vào file cache trên, đầu tiên bạn thêm thư mục chứa library đó vào file /etc/ld.so.conf, đây là file cấu hình chứa các thư mục sẽ được tạo index bởi tiện ích ldconfig. Sau đó, chạy lệnh ldconfig với cú pháp như sau:

	# ldconfig [options] lib_dirs
	chỉ có 2 options để lựa chọn
	-p chỉ hiện thị nội dung hiện tại của cache, không tạo tại cache
	-v: hiển thị quá trình thực hiện việc tạp lại cache
Để xem nội dung của ld.so.cache : ldconfig -p

![hinh 29](https://user-images.githubusercontent.com/74639473/100116911-db157f00-2ea6-11eb-972a-0be6b4a86591.png)

Để tìm kiếm một library trong cache : ldconfig -p | grep < ...>

![hinh 210](https://user-images.githubusercontent.com/74639473/100116927-dd77d900-2ea6-11eb-99ba-c28dda9a58fd.png)

tạo lại cache :ldconfig : 

[trở về mục luc](#mucluc)

<a name="P23"> </a>

## 2.3. Scheduling processes with cron

### 2.3.1. Định nghĩa về Cron

Cron là một tiện ích giúp lập lịch chạy những dòng lệnh bên phía server để thực thi một hoặc nhiều công việc nào đó theo thời gian được lập sẵn. Một số người gọi những công việc đó là Cron job hoặc Cron task.

Cron là một chương trình daemon, tức là nó được chạy ngầm mãi mãi một khi nó được khởi động lên. Như các daemon khác thì bạn cần khởi động lại nó nếu như có thay đổi thiết lập gì đó. Chương trình này nhìn vào file thiết lập có tên là crontab để thực thi những task được mô tả ở bên trong.

Lợi ích của việc sử dụng Cron:

	có thể cài đặt để cron job thực thi việc quét xem những user trial nào đã bị expired và delete hoặc set inactive tài khoản của họ
	Gửi đi những email tới user sử dụng hệ thống hàng ngày hay hàng tuần
	Xoá bỏ những file cache hàng tháng khi nó quá lớn
	Kiểm tra hàng ngày xem những link nào của website bị hỏng hay không để nhanh chóng khắc phục
	Backup cơ sở dữ liệu
	
### 2.3.2. Sử dụng Cron

- cú pháp của Cron

Ví dụ :

	20 *  * * * /usr/bin/php /var/html/dailycron.php > /home/yourname/logs/phplogs.txt

Đây là đoạn mô tả cron task đơn giản, nó có 2 phần chính

Đoạn 20  *  * * * là nơi chứa cài đặt về lịch biểu chạy của cron, chi tiết ở mục sau.

Đoạn sau đó là những lệnh sẽ thực thi. Tùy mục đích mà có thể cài đặt khác nhau:

/usr/bin/php vì mã mã PHP không thể tự chạy mà phải có parser nên có khai báo này giúp cron biết nó cần vào đâu để tìm

/var/www/html/dailycron.php/ là đường dẫn đến file chứa các mã lệnh PHP sẽ được chạy.

> /home/yourname/logs/phplog.txt là nếu cần output của các mã lệnh thì sẽ khai báo xem chúng sẽ được ghi ở đâu

- Cú pháp cài đặt lịch biểu

Như ví dụ ở trên, lịch biểu của Cron bao gồm 5 phần có thể khai báo hoặc không, cụ thể là :

	phút : giá trị từ 0 đến 59
	giờ : giá  trị từ 0 đến 23
	ngày của tháng : từ 1 đến 31
	tháng : từ tháng 1 đến 12
	ngày trong tuần: từ 0 (chủ nhật) đến 6 (thứ 2)
	
Nếu không khai báo số cụ thể thì khai báo *, kí hiệu thay cho các con số trên. Có thể một số ký  hiệu khác có ý nghĩa như  sau:

	dùng dấu phẩy để thiết lập số cho nhiều thời điểm.
	dùng dấu / để chia điều khoảng cách thời gian được chạy
	dùng dấu  - để chỉ khoảng thời gian
	@reboot để chạy lệnh nào đó khi server boot lại
	@hourly : chạy hàng giờ vào phút thứ 0
	@daily : chạy hàng ngày vào 00:00
	@monthy: chạy hàng tháng vào 00:00 của ngày đầu tiên của tháng
	@yearly chạy hàng năm vào 00:00 của ngày đầu tiên của năm
	
### 2.3.3. Những lệnh và khai báo cơ bản của Cron task.

Dùng lệnh crontab -e để  mở file crontab rồi thiết lập những lịch biểu

Nếu có nhiều texteditor thì sẽ có xác nhận xem chọn cái nào để chỉnh sửa file crontab. Và nếu bạn chưa từng thiết lập gì trước đó thì 1 file mới sẽ được tạo với nội dung trống (có thể có nhưng chỉ là comment của Cron, bắt đầu bằng dấu #). Hãy thử điền nội dung và save lại.

Để xem nội dung file có những gì : crontab -l

Nếu muốn xóa nội dung file thiết lập crontab đi thì dùng : crontab -r

Nếu mỗi lần chạy với > thì kết quả sau sẽ ghi đè lên kết quả chạy trước. Do đó nếu muốn lấy kết quả của tất cả các lần chạy thì bạn cần phải append vào file bằng cách dùng 2 dấu >>. Nhưng lưu ý là file của bạn có thể sẽ rất lớn nếu cron task được chạy hàng ngày.

Khai báo


	SHELL là shell mà cron chạy. Nếu không có chỉ định thì  mặc định sẽ những  shell ở trong file /etc/passwd
	PATH: để dùng đi dùng lại nhiều lần
	MAILTO: sẽ chỉ định xem ai sẽ nhận được email về những output của mỗi lệnh. Nếu không có chỉ định thì output sẽ được gửi đến người sở hữu tiến trình mà tạo ra output đó.
	HOME: là thư mục home  được sử dụng cho cron. Nếu không khai báo thì mặc định là thư mục /etc/passwd
[trở về mục lục](#mucluc)

<a name="P24"> </a>
## 2.4 rontab command options

Crontab là danh sách các tác vụ được lên lịch chạy theo các khoảng thời gian đều đặn trên hệ thống.

Daemon đọc crontab và thực hiện các lệnh vào đúng thời điểm được gọi là cron

Lệnh crontab được sử dụng để xem hoặc chỉnh sửa bảng lệnh được chạy bởi cron.

## 2.4.1 Cú pháp

crontab [-u user] file

crontab [-u user] [-l | -r | -e] [-i] [-s]
- option

options | chức năng |
------- | ----------|
file | Tải dữ liệu crontab từ tệp được chỉ định. Nếu tệp là một dấu gạch ngang ("-"), thì dữ liệu crontab được đọc từ đầu vào chuẩn.|
-u user | Chỉ định người dùng có crontab sẽ được xem hoặc sửa đổi. Nếu tùy chọn này không được đưa ra, crontab sẽ mở crontab của người dùng đã chạy crontab. Lưu ý: sử dụng su để chuyển đổi người dùng có thể nhầm lẫn với crontab, vì vậy nếu bạn đang chạy nó bên trong su, hãy luôn sử dụng tùy chọn -u để tránh bị sai. |
-l | hiện thi crontab hiện tại |
-r | xóa crontab hiện tại |
-e | Chỉnh sửa crontab hiện tại, sử dụng trình chỉnh sửa được chỉ định trong biến môi trường VISUAL hoặc EDITOR |
-i | Tương tự như -r, nhưng cung cấp cho người dùng lời nhắc xác nhận có / không trước khi xóa crontab. |
-s | Chỉ SELinux: nối chuỗi ngữ cảnh bảo mật SELinux hiện tại dưới dạng cài đặt MLS_LEVEL vào tệp crontab trước khi tiến hành chỉnh sửa hoặc thay thế. Xem tài liệu SELinux của bạn để biết thông tin chi tiết. |


### 2.4.2 OverView

Lệnh crontab được sử dụng để xem hoặc chỉnh sửa bảng lệnh được chạy bởi cron.

Mỗi người dùng trên hệ thống của bạn có thể có một crontab cá nhân.

Các tệp Crontab được đặt trong /var/spool/ (hoặc một thư mục con như /var/spool/cron/crontabs), nhưng chúng không nhằm mục đích chỉnh sửa trực tiếp. Thay vào đó, chúng được chỉnh sửa bằng cách chạy crontab.

- Crontab command entries

Mỗi mục nhập lệnh cron trong tệp crontab có năm trường thời gian và ngày tháng (theo sau là tên người dùng, chỉ khi đó là tệp crontab của hệ thống), theo sau là một lệnh.

Các lệnh được thực thi bởi cron khi các trường phút, giờ và tháng khớp với thời gian hiện tại và ít nhất một trong hai trường ngày (ngày trong tháng hoặc ngày trong tuần) khớp với ngày hiện tại.

Trình nền cron kiểm tra crontab mỗi phút một lần.

ví du:

 chạy tập lệnh shell /home/melissa/backup.sh vào ngày 2/6 lúc 7h10
 	
	10 7 2 6 /home/melissa/backup.sh
	
Cũng như ví dụ trên , chạy tập lệnh vào 12h30 vào thứ 2 hàng tuần tháng 6
	
	30 12 * Jun Monday /home/melissa/backup.sh
	
Chạy /home/carl/hourly-archive.sh hàng giờ, vào giờ, từ 9 giờ sáng(09:00) đến 6 giờ chiều (18:00), mỗi ngày:

	00 09-18 * * * /home/carl/hourly-archive.sh
	
Tương tự như ở trên, nhưng chạy nó hai mươi phút một lần

	*/20 09-18 * * * /home/carl/hourly-archive.sh


- Trường ngày và giờ

field | allowed values |
------| ------------|
minute | 0- 59 |
hour | 0 -23 |
day of month | 0 -31 |
month | 0 -12 |
day of week | 0 -7 |

- Contab file 

Mỗi dòng của tệp crontab là "hoạt động" hoặc "không hoạt động". Dòng "hoạt động" là cài đặt môi trường hoặc mục nhập lệnh cron. Dòng "không hoạt động" là bất kỳ thứ gì bị bỏ qua, bao gồm cả nhận xét.

Các dòng trống và khoảng trắng ở đầu và tab bị bỏ qua. Các dòng có ký tự không phải khoảng trắng đầu tiên là dấu thăng (#) được hiểu là các chú thích và bị bỏ qua. Chú thích không được phép trên cùng một dòng với lệnh cron, vì chúng sẽ được hiểu là một phần của lệnh. Vì lý do tương tự, nhận xét không được phép trên cùng một dòng với cài đặt biến môi trường

- Môi trường thiết lập 

Một dòng thiết lập môi trường trong crontab có thể đặt các biến môi trường cho bất cứ khi nào cron chạy một công việc.

Môi trường thiết lập crontab được định dạng như sau:
	
	name = value

Các khoảng trắng xung quanh dấu bằng (=) là tùy chọn và bất kỳ khoảng trắng nào không đứng đầu sau đó trong giá trị sẽ là một phần của giá trị được gán cho tên. Chuỗi giá trị có thể được đặt trong dấu ngoặc kép (đơn hoặc kép, nhưng phù hợp) để giữ các khoảng trống ở đầu hoặc cuối.

Một số biến môi trường được đặt tự động bởi cron:

	SHELL được đặt thành / bin / sh.
	LOGNAME và HOME được đặt từ dòng / etc / passwd của chủ sở hữu crontab. 
	HOME và SHELL có thể bị ghi đè trong thời gian chạy bởi các cài đặt trong crontab; LOGNAME có thể không.
	Biến LOGNAME đôi khi được gọi là USER trên hệ thống BSD. Trên các hệ thống này, USER cũng sẽ được đặt.
	
### 2.4.3. Cấu hình

- Cho phép người dùng chạy cron

Các công việc Cron có thể được phép hoặc không được phép đối với người dùng cá nhân, như được định nghĩa trong các tệp /etc/cron.allow và /etc/cron.deny. Nếu cron.allow tồn tại, một người dùng phải được liệt kê ở đó để được phép sử dụng một lệnh nhất định. Nếu tệp cron.allow không tồn tại nhưng tệp cron.deny thì có, thì người dùng không được liệt kê ở đó để sử dụng lệnh đã cho. Nếu không có tệp nào trong số các tệp này tồn tại, chỉ người dùng cấp cao mới được phép sử dụng một lệnh nhất định.

Các quyền của cron cũng có thể được xác định bằng cách sử dụng xác thực PAM (mô-đun xác thực có thể cắm được) để thiết lập người dùng có thể hoặc không sử dụng crontab và công việc cron hệ thống. Cấu hình PAM nằm trong /etc/cron.d/. 

- Cấu hình thư mục tạm thời

Thư mục tạm thời cho các công việc cron có thể được đặt trong các biến môi trường được liệt kê bên dưới. Nếu các biến này không được xác định, thư mục tạm thời mặc định / tmp được sử dụng

[trở về mục lục](#mucluc)

<a name="P3"> </a>
# 3. SYSTEM SECURITY AND ENCRYPTION

<a name="P31"> </a>
## 3.1. The secure shell OpenSSH

### 3.1.1 Khái niệm SSH

SSH (Secure Shell) là giao thức mạng dùng để thiết lập kết nối mạng một cách bảo mật. SSH hoạt động ở lớp ứng dụng của phân lớp TCP/IP. 

Các công cụ SSH (như OpenSSH, PuTTy, etc.) cung cấp cho người dùng cách thức để thiết lập kết nối mạng được mã hóa để tạo kênh kết nối riêng tư.

Tính năng tunneling (port forwarding) cho phép chuyển tải các giao vận theo các giao thức khác.

### 3.1.2. Một số khái niệm liên quan

-  Encryption

a. Shared secrets

Khái niệm chỉ sự chia sẻ thông tin bí mật, trường hợp thường thấy là sử dụng "password" ở cả 2 phía để mã hóa và giải mã.

b. Public keys

Thực chất có một cặp keys. Thông tin bị mã hóa với một key nhưng có thể giải mã với một key khác. Thông thường một key sẽ giữ bí mật trong khi key kia sẽ được phân tán công khai.

** Public key authentication

Nếu bạn có public key, bạn có thể sử dụng để kiểm tra liệu các đầu cuối khác có đang giữa private key không
** Fingerprints

Khi nhận được một public key, ta không biết được nó có thuộc về người mà ta muốn "nói chuyện" hay không. Phương thức để xác nhận keys ở đây là thông qua fingerprints. Nếu có được fingerprint ahead của key, ta có thể kiểm tra lại lần nữa key đang giữ.

- Truy cập máy tính từ xa

a. Truy cập cục bộ

Thực hiện các command trực tiếp trên shell mọi thứ ta gõ lên terminal

b. Telnel: mô phỏng tương tự như truy cập cục bộ nhưng thông qua mạng. Mọi thứ ta gõ đều có thể lấy được, gửi qua mạng và gửi tới shell trên máy remote. Telnet gửi mọi thứ thông qua mạng và mọi người đều có thể thấy được chính xác những gì ta đã gõ, bao gồm cả việc ta thấy gì trên màn hình (ví dụ password)

c. SSH: ý tưởng tương tự telnet, lấy những gì ta gõ và gửi qua mạng tới shell trên máy remote, truy nhiên thông tin trước khi gửi đi được mã hóa. Mọi người theo dõi phiên ssh trên mạng sẽ chỉ thấy những ký tự vô nghĩa

### 3.1.3. Cấu hình SSH
 
 #### Bước 1. Tiến hành cấu hình trên Server
 
 Theo mặc định, khi tiến hành cài Linux trên Server, SSH sẽ được đi kèm trong hệ điều hành.
 
 Kiểm tra SSH bằng câu lệnh
 	
	#service sshd retstart
![30](https://user-images.githubusercontent.com/74639473/100179465-b18d3f80-2f08-11eb-8b0e-95388f904096.png)

Nếu trả về kết quả starting sshd [ok] là đã có SSH rồi

Nếu chưa có sử dụng lênh sau để cài đặt

	#yum install openssh-server -y
	
![31](https://user-images.githubusercontent.com/74639473/100179478-b6ea8a00-2f08-11eb-8407-ea6109781c3c.png)

#### Bước 2. Trên máy Client tiến hành cài đặt phần mềm truy cập SSH (MobaXterm)

Để cài đặt MobaXterm  truy cập vào https://mobaxterm.mobatek.net/download-home-edition.html

Sau khi cài đặt xong MobaXterm thì thực hiện SSH .

Bấm vào nút SSH sẽ hiện thị giao diện sao và thêm các thông số sau: 
Hostname (or IP address ) : Nhập IP của Server
Port : 22 ( Đây là port mặc định của SSH )
Sau đó ta bấm Open

![32](https://user-images.githubusercontent.com/74639473/100181056-10a08380-2f0c-11eb-8666-6137cc70749b.png)

Sau đó hiện thị thông tin user và cần nhập password để thực hiện truy cập từ xa thông qua SSH

![33](https://user-images.githubusercontent.com/74639473/100181064-14340a80-2f0c-11eb-9ae0-3d297d0a4112.png)

Vậy là quá trình truy cập SSH đã thành công!

-  một số thư mục cấu hình mặc định có trong SSH :

	/etc/ssh/sshd_config : Cấu hình OpenSSH Server
	/etc/ssh/ssh_config : Cấu hình OpenSSH Client
	~/.ssh/ : Thực mục SSH user
	~/.ssh/authorized_keys hoặc ~/.ssh/authorized_keys : Thư mục chứa public key (RSA hoặc DSA) dùng để cấu hình SSH auth
	/etc/nologin : Nếu file này tồn tại , SSH sẽ từ chối mọi user trừ root
	/etc/host.allow và /etc/hosts.deny : Thư mục AcessList của SSH
	
### Bước 3. Cấu hình nâng cao

Truy cập vào file cấu hình của SSH: 
	
	#vi /etc/ssh/sshd_config
- 1. Đổi Port SSH: (số port >1024)

Mặc định dịch vụ SSH sử dụng port 22, bạn nên thay đổi cổng mặc định này để tránh các mối nguy hại từ các cuộc tấn công khai thác qua port 22.

ví dụ port =2222

![](./Images/Report2/34.png)

Sau đó, tiến hành mở port trên firewall vầ đổi port 22 thành 2222

	#vi /etc/sysconfig/iptable
	
Tiếp đến restart lại ssh và firewall

	#service sshd restart
	#service iptable restart

- 2. Giới hạn IP truy cập SSH

Trong iptable, tiến hành đặt rule cho phép 1 ip truy cập cổng SSH
VD . IP client là 192.168.1.168 được phép SSH vào server với port  2222, còn các ip khác không đượ phép truy cập vào port đấy sẽ bị DROP

  	-A INPUT -m state –state NEW -m tcp -p tcp –dport 2222 -s 192.168.1.168 -j ACCEPT
	-A INPUT -m state –state NEW -m tcp -p tcp –dport 2222 -j DROP	

![](./Images/Report2/38.png)

- 3. Giới hạn số lần đăng nhập sai và số phiên SSH

Giới hạn số lần đăng nhập sai:

MaxAuthTries 3 //Sau 3 lần đăng nhập sai sẽ thoát khỏi session, dùng để tránh dò mật khẩu

MaxSessions 1 //Chỉ được phép duy nhất 1 phiên kết nối SSH

![](./Images/Report2/39.png)

- 4. Tắt truy cập bằng tài khoản root

root là user có quyền cao nhất trong server. Nếu bạn là một quản trị máy chủ web đang sử dụng máy chủ Linux (Centos, Ubuntu …), chắc hẳn bạn sẽ gặp phải trường hợp tài khoản root của mình bị tấn công bruteforce mật khẩu (nôm na là dò mật khẩu) qua SSH. Những cuộc tấn công bruteforce này có thể kéo dài hàng tháng thậm chí tới hàng năm bởi các hệ thống tấn công tự động và rất mạnh mẽ. Nếu tài khoản root không được cài đặt một password đủ mạnh, hacker có thể dễ dàng chiếm được quyền cao nhất của hệ thống.

Vì vậy, tốt hơn hết bạn nên sử dụng một tài khoản riêng khác để đăng nhập SSH vào hệ thống và vô hiệu hóa quyền SSH qua root trên máy chủ.

Truy cập vào file sshd_config, và chỉnh 

![](./Images/Report2/310.png)

Nếu server có nhiều user, cấu hình Deny và allow

![](./Images/Report2/311.png)

- 5. Sử dụng SSH  Protocol 2

SH có hai version giao thức, giao thức cũ Protocol 1 kém an toàn nên có một giao thức mới giúp tăng khả năng đảm bảo bảo mật trên đường truyền đó là Protocol 2. Chính vì vậy OpenSSH luôn sử dụng Protocol 2 để sử dụng cho các máy chủ SSH để đảm bảo an toàn

So sánh giữa protocol 1 và protocol 2

protocol 2 | protocol 1 |
---------| --------|
Các giao thức vận chuyển, kết nối và xác thực tách biệt |  Sử dụng một giao thức đơn khối|
Sử dụng thuật toán kiểm tra tính toàn vẹn mạnh| Sử dụng CRC-32 để kiểm tra tính toàn vẹn yếu |
 Hỗ trợ thay đổi mật khẩu | không hỗ trợ |
  Sử dụng các phương thức xác thực sau:
public key (DSA, RSA*, OpenPGD) hostbased password | Hỗ trợ nhiều phương thức: public key (RSA) RhostRSA password TIS Kerberos |
Sử dụng thuật toán Diffie-Hellman để phân phối khóa loại bỏ sự phụ thuộc vào một server key | Server key được sử dụng để chuyển tiếp khóa phiên|
Hỗ trợ chứng chỉ Public-key | không hỗ trợ | 
trao đổi chứng thực người dùng linh hoạt hơn, cho phép yêu cầu nhiều hình thức xác thực để truy cập | Chỉ cho phép một kiểu xác thực cho mỗi phiên |
Thay thế các khóa phiên theo định kỳ | không hỗ trợ |

- 6. Cấu hình Logout SSH nếu phiên đang Idle

Trong file sshd_config :

	ClientAliveInterval 300 //Sau 300s = 5’ , sẽ bị logout nếu phiên đang Idle
	ClientAliveCountMax 0 //Khoảng thời gian chờ nếu không có dữ liệu sẽ gửi 1 thông điệp yêu cầu phản hồi

- 7. Bật cảnh báo trong Banner

vi /etc/bannerssh

them canh cao

Tiếp tục truy vấn
vi /etc/ssh/sshd_config

![](./Images/Report2/312.png)

- 8 sử dụng mật khẩu mạnh

Việc đặt mật khẩu mạnh giúp hạn chế việc bị dò password.

Để tạo thư viện “Random Password” bằng cách

	#vi ./báh_generate
thêm dòng sau vào: 

	genpasswd() {

	local l=$1

	[ “$l” == “” ] && l=20

	tr -dc A-Za-z0-9_ < /dev/urandom | head -c ${l} | xargs

	}

Chạy tiếp lệnh thực thi 
	
	#source ./bash_generate
	# genpasswd

![](./Images/Report2/314.png)

- 9. Cấu  hình SSH key

Sử dụng 2 cặp khóa là Public Key và Private key. Đây là một phương pháp khá an toàn giúp đảm bảo kết nối SSH

![](./Images/Report2/315.png)

Cấu hình SSH Key chi tiết   [tại đây](#P32)

[trở về mục lục](#mucluc)

<a name="P32"></a>
	 
## 3.2 Public/private key authentication

### 3.2.1. Giới thiệu về SSH key

Tuy SSH là giao thức mã hóa an toàn, tuy nhiên ta vẫn phải sử dụng mật khẩu để truy cập. Và nếu trong quá trình sử dụng mà các gói tin của bạn bị bắt lại, các phiên trao đổi khóa giữa SSH server và client sẽ bị lộ và attacker có thể dùng nó để giải mã dữ liệu. Hơn nữa, việc này cũng tạo điều kiện cho các cuộc tấn công Brute Force mật khẩu.

SSH hỗ trợ sử dụng cặp khóa Private Key và Public Key được chia sẻ với nhau từ trước. Nghĩa là đã có sẵn Private Key để trao đổi với server mà không cần đến quá trình trao đổi khóa, điều này sẽ hạn chế khả năng bị bắt gói. Hơn nữa cặp khóa này còn có một mật khẩu riêng của nó, gọi là passphrase (hay keyphrase). Mật khẩu này được dùng để mở khóa Private Key (được hỏi khi bạn SSH vào server) và tạo lớp xác thực thứ 2 cho bộ khóa. Nghĩa là:

+ Nếu attacker không có Private Key, việc truy cập vào server gần như không thể, chỉ cần bạn giữ kĩ Private Key.

+ Tuy nhiên trong trường hợp Private Key bị lộ, bạn vẫn khá an toàn vì đối phương không có passphrase thì vẫn chưa thể làm được gì, tuy nhiên đó chỉ là tạm thời. Bạn cần truy cập server thông qua cách trực tiếp hoặc qua VNC của nhà cung cấp nếu đó là một VPS để thay đổi lại bộ khóa.

### 3.2.2. Cấu hình SSH key

- tạo khóa trên server

tạo 1 cặp khóa key bằng câu lệnh sau:
	
	-ssh-keygen -t rsa

![](./Images/Report2/h320)

theo mặc định, khi sử dụng câu lệnh trên OpenSSH sẽ sử dụng RSA 2048bit

	/root/.ssh/id_rsa : đây là file chứa Private key
	/root/.ssh/id_rsa.pub : Đây là file chưa Public key
	Passphrase : mật khẩu đi cùng để mã hóa/ giải mã private key


- chỉnh sửa cấu hình  trên server

 Vào #vi /etc/ssh/sshd_config
 
 thay đổi các dòng sau :
 
	 RSAAuthentication yes

	PubkeyAuthentication yes

	AuthorizedKeysFile      /root/.ssh/id_rsa.pub
 
	PasswordAuthentication no

- Copy nội dung private key ra máy tính, khởi động lại ssh

dùng lệnh cat để xem nội ding private key đã tạo ở mục mặc định : cat /root/.ssh/id_rsa

![](./Images/Report2/h322.png)

coppy toàn bộ nội dung ra file  notepad

Sau đó, tiến hành khởi động lại sshd : #service sshd restart

### 3.2.3. thực hiện cài đặt trên Client window

Sau khi coppy key về máy tính và lưu lại tên ( mình lưu là id_rsa)

- load key với MobaXterm 

trên MobaXterm, click vào tab Tools  , kích vào MobaKeyGen

![](./Images/Report2/h323.png)

tiếp đến chọn Load để load private key

![](./Images/Report2/h324.png)

Chọn đường dẫn đến file copy key từ server đã lưu ở phần trên. Sau đó sẽ có 1 của sổ hiện ra như bên dưới, nhập vào mật khẩu Passphrase sau đó chọn Ok để tiếp tục.

![](./Images/Report2/h325.png)

Như vậy ta đã load xong. Tiếp tục chọn Save private key để lưu lại private key, sau này dùng cho việc ssh.

![](./Images/Report2/h326.png)

- kiểm tra sử dụng ssh key

Để kiểm tra, ta cần sử dụng key và ssh vào server. Hãy làm theo các bước sau :

Trên server chọn tab Session sau đó chọn New session

Tại đây làm theo các bước sau :

Chọn giao thức là SSH

Nhập vào IP của server để tiến hành ssh đến

Tích chọn sử dụng private key để xác thực ssh bằng key

Sau đó chọn vào biểu tượng file để chọn đường dẫn private key.

![](./Images/Report2/h327.png)

Chọn private key vừa lưu có tên là privateky_user sau đó chọn Open để xác nhận.

Sau khi chọn file, chọn Ok để bắt đầu ssh tới server

![](./Images/Report2/h328.png)

Ta sẽ ssh vào bằng user thuctap, sau khi nhập vào user ta sẽ không xác thực bằng mật khẩu của user đó mà sẽ sử dụng mật khẩu của Passphrase key.

![](./Images/Report2/h321.png)

Sau khi nhập mật khẩu, ta sẽ login được vào bằng user và thao tác như bình thường.

![](./Images/Report2/h329.png)

Vậy quá trình cấu hình ssh  key đến đây là thành công và bắt đầu sử dụng máy tính từ xa

 [trở về mục lục)(#mucluc)
 
 <a name="P33"> </a>
 ## 3.3.  X11 forwarding
 
 ### 3.3.1. X11 forwarding là gì? 
 
 X11 forwarding là phương pháp cho phép người dùng khởi động ứng dụng đồ họa được cài đặt trên hệ thống Linux từ xa và chuyển tiếp các cửa sổ ứng dụng (màn hình) đó đến hệ thống cục bộ. Hệ thống từ xa không cần phải có máy chủ X hoặc môi trường máy tính để bàn đồ họa. Do đó, việc định cấu hình chuyển tiếp X11 bằng SSH cho phép người dùng chạy các ứng dụng đồ họa một cách an toàn qua phiên SSH.

Để diễn đạt điều này trong thuật ngữ chuyên môn,

Chúng được kết nối với hệ thống từ xa qua SSH,

Và sau đó chúng khởi chạy ứng dụng GUI (được cài đặt trong hệ thống từ xa) từ phiên SSH đó,

Bây giờ, ứng dụng GUI chạy trên hệ thống từ xa, nhưng cửa sổ ứng dụng xuất hiện trên hệ thống cục bộ . Vì vậy,có thể sử dụng chương trình GUI từ xa này trên hệ thống cục bộ của bạn như cách chúng tôi sử dụng chương trình được cài đặt cục bộ.

### 3.3.2. Cấu hình X11 forwarding  trong SSH linux

Trước khi cấu hình X11 forwarding thì cần phải cài đặt 'xauth' từ hệ thống tìm kiếm  từ xa. Nếu chưa cài đặt thì  sử dụng câu lênh sau để cài đặt : # yum install xorg-x11-xauth

![](./Images/Report2/330.png)
Để thử nghiệp X11 forwarding xem có hoạt động không. Ta cài đặt : xeyes

![](./Images/Report2/333.png)

Tiếp đến chỉnh sủa trong file ssh_cònig và thêm X11Forwarding yes

![](./Images/Report2/331.png)

Khởi động lại ssh : # service sshd restart

- Cấu hình X11 forwarding trên mobaXterm

![](./Images/Report2/332.png)

Nhập tên người dùng và mật khẩu của máy chủ từ xa. Sau khi bạn kết nối với hệ thống từ xa qua mobaXterm, hãy khởi chạy bất kỳ ứng dụng X nào được cài đặt trong máy chủ từ xa.

Để thử nghiệp x11 forwarding , sử dụng câu lệnh xeyes. Nếu xuất hiện đôi mắt ở tại con trỏ xung quanh màn hình

![](./Images/Report2/334.png)

Bây giờ đã thấy nó hoạt động, có lẽ đã đến lúc chia sẻ cách hoạt động của tất cả.

Bất kể sử dụng GUI nào trên máy chủ Linux, GNOME hay KDE, cả hai đều có cái được gọi là trình quản lý xdisplay làm nền tảng cho phần GUI của màn hình. Nó là một giao thức mạng được thiết kế ngay từ đầu để cho phép các mục được chuyển tiếp đến bất kỳ đích nào được yêu cầu

[trở về mục lục](#mucluc)

<a name="P4"> </a>
# 4. BACKUP AND RESTORE

<a name="P41"> </a>
##  4.1. Archiving with tar

### 4.1.1. Khái niệm và ưu điểm của Tar

- Khái niệm

Tar là chữ viết tắt của Tape archive, là một lệnh phổ biến nhất để nén và giải nén file và thư mục trong Linux. Có rất nhiều lợi ích khi dùng lệnh tar, người dùng chuyên nghiệp rất thích lệnh này.

Trong hầu hết các trường hợp, nén file bằng tar sẽ cho kết quả là file .tar. Còn nén mạnh hơn thì dùng gzip với file sau cùng là .tar.gz.

Với tar command, bạn có thể nén và giải nén file. Tar có nhiều lựa chọn cách thực hiện hành động nên có thể bạn cần nhớ những tùy chọn này.

- Ưu điểm của Tar linux

Tar, nói về việc nén thì nó có khả năng nén 50%, tức là khá hiệu quả

Giảm thiểu kích cỡ của file và folder

Tar không thay đổi tính năng của file và thư mục. Quyền và những tính năng khác không bị ảnh hưởng sau khi nén

Tar được dùng rộng khắp mọi phiên bản Linux. Nó cũng dùng được trên firmware Android và được hỗ trợ bởi những hệ điều hành Linux cũ hơn nữa.

Nén và giải nén nhanh

Dễ sử dụng

- Khi nào cần sử dụng Tar command

Nếu làm việc với hệ điều hành Linux và cần nén file và giải nén file

Cần chuyển file và thư mục lớn từ server này sang server khác

Tạo backup cho website, data,...

Giảm dung lượng sử dụng của hệ điều hành xuống, vì sau khi nén nó sẽ tiết kiệm được không gian

Upload và download thư mục.

### 4.1.2. Cấu hình sử dụng Tar

- Tạo file nén Tar

 + Tạo filec .tar linux
Bạn có thể nén file nén .tar cho file và thư mục. Cấu trúc để lệnh tar để nén file như sau:
	
	tar -cvf <filename .tar > /đường dẫn đến file bị nén 
	trong tùy chon -cvf có nghĩa  là :
		 c- : tạo file .tar mới
		 v : hiện thị quá trình nén lên màn hình
		 f : tên file
ví dụ:  tar  -cvf /home/attt/TestTar/Test1.tar /home/attt/TestTar/Test1

![](./Images/Report2/40.png)

 + Tạo file nen .tar.gz trong linux
 
 Nếu bạn muốn nén mạnh hơn, bạn cũng có thể dùng file .tar.gz. Cấu trúc cơ bản để nén file bằng lệnh tar là:
 
	tar -cvzf <filename .tar > /đường dẫn đến file bị nén
	Tùy chọn z có nghĩa là sử dụng gzip để nén, cách nén này cho file ra còn nhỏ hơn. Ngoài ra, tên file cũng có thể đặt .tgz
	
ví dụ : tar -cvzf /home/attt/TestTar/Test1.tar.bz /home/attt/TestTar/Test1

![](./Images/Report2/41.png)

 + tạo file nén .tar.bz2 trong Linux băng tar
 
File .bz2 sẽ nén nhiều hơn so với gzip. Tuy nhiên, nó cần nhiều thời gian hơn để nén và giải nén. Để nén file .tar.bz2, bạn sẽ cần thêm tùy chọn -j. Ví dụ như sau :  tar -cvjf /home/attt/TestTar/Test1.tar.bz2 /home/attt/TestTar/Test1

![](./Images/Report2/42.png)

- Giải nén Tar

Lệnh tar cũng có thể dùng để giải nén file. Cấu trúc lệnh giải nén như sau:

	tar -xvf /home/attt/TestTar/Test1.tar
	
Nếu bạn muốn giải nén sang thư mục khác hãy dùng tùy chọn -C, ví dụ như sau	

	tar -xvf /home/attt/TestTar/Test1.tar -C /home/attt/

Lệnh giải nén file .tar.gz cũng tương tự:

	tar -xvf /home/attt/TestTar/Test1.tar.gz
	tar -xvf /home/attt/TestTar/Test1.tar.gz -C /home/attt/
	
và để giải nén .tar.bz2 .tar.tbz hoặc .tar.tb2 thì dùng cấu trúc sau:

	tar -xvf /home/attt/TestTar/Test1.tar.bz2

- Liệt kê nội dung của file nén linux

Sau khi file archive được tạo, bạn có thể liệt kê nội dung của nó ra bằng lệnh tar theo cấu trúc: 
	
	tar -tvf /home/attt/TestTar/Test1.tar

Nó sẽ hiển thị toàn bộ danh sách file và mốc thời gian, cùng quyền hạn của file. Tương tự, đối với file .tar.gz, .tar.bz2 bạn dùng lệnh sau:
	
	tar -tvf /home/attt/TestTar/Test1.tar.gz
	tar -tvf /home/attt/TestTar/Test1.tar.bz2

![](./Images/report2/44.png)

- Giải nén 1 file trong file .tar

	tar  -xvf  /home/attt/TestTar/Test1.tar test1.txt
ở đây file test1.txt là file lẻ được giải nến bên trong file Test1.tar
hoặc sử dụng lệnh :
	
	tar --extract --file= .tar example.sh

- giải nén nhiều file trong file .tar

	 - tar -xvf <file .tar.gz> "file1" "file2"
	 - tar -xvf <file .tar> "file1" "file2"
	 - tar -xvf <file .tar.bz2> "file1" "file2"

- Giải nén nhiều file theo mẫu

Nếu muốn giải nén nhiều file chỉ có định dạng theo mẫu, nên dùng wildcards.

Ví dụ :
	
	tar -xvf /home/attt/TestTar/Test1.tar --wildcards '*.jpg'
	tar -zxvf /home/attt/TestTar/Test1.tar.gz --wildcards '*.jpg'
	tar -jxvf /home/attt/TestTar/Test1.tar.bz2 --wildcards '*.jpg'

- thêm file vào file .tar

Để làm vậy, bạn sẽ cần dùng tùy chọn -r (viết theo từ append – thêm vào). Tar có thể thêm cả file và thư mục vào.

Ví dụ: 
	
	tar -rvf home/attt/TestTar/Test1.tar newtest.jpg
	
Để  thêm thư mục vào là:

	tar -rvf sampleArchive.tar new
Không thể thêm file hay thư mục vào file .tar.gz hoặc .tar.bz2

- Để xem kích thước file

Sau khi tạo một file archive, bạn cũng có thể xem kích thước của nó. Kích thước sẽ được hiển thị dưới dạng KB (Kilobytes).

Bên dưới là ví dụ kiểm tra kích thước của nhiều file archive khác nhau:

tar -czf - /home/attt/TestTar/Test1.tar | wc -c

tar -czf - /home/attt/TestTar/Test1.tar.gz | wc -c

tar -czf - /home/attt/TestTar/Test1.tar.bz2 | wc -c

- Nối 2 tập tin .tar

Chúng ta có thể dễ dàng hợp nhiều tập tin tar với nhau bằng tùy chọn -A. Giả sử rằng chúng ta có 2 tarball là file1.tar và file2.tar. Chúng ta có thể nối nội dung của file2.tar vào file1.tar như sau

	tar -Af file1.tar  file2.tar
	
Kiểm tra nó bằng cách liệt kê nội dung:

	tar -tvf file1.tar 

- So sánh các tập tin trong tar với bên ngoài 

Chúng ta có thể so sánh các tập tin bên trong tập tin lưu trữ với các tập tin có cùng tên nằm bên ngoài, trong cùng 1 hệ thống tập tin để kiểm tra xem chúng có giống nhau hoặc có điểm gì khác nhau không. Việc này đôi khi rất hữu ích khi chúng ta cần kiểm tra xem 1 tập tin nào đó đã được đưa vào tập tin lưu trữ chưa.

Để thực hiện việc so sánh này, sử dụng tùy chọn -d trong lệnh tar. Cờ -d sẽ in ra các điểm khác nhau như sau: tar -df file.tar

- xóa tập tin trong file .tar

Có thể xóa tập tin  bên trong file .tar bằng cách sử dụng câu lệnh detete như sau: 

	$ tar -f file.tar --delete file1 file2 ..

[trở về mục lục](#mucluc)

<a name="P42"> </a>
## 4.2 Using the dd command

### 4.2.1. khái niệm

Câu lệnh dd trong linux là một trong những câu lệnh thường xuyên được sử dụng. Câu lệnh dd dùng để sử dụng trong các trường hợp sau:

- Sao lưu và phục hồi toàn bộ dữ liệu ổ cứng hoặc một partition 
- Chuyển đổi định dạng dữ liệu từ ASCII sang EBCDIC hoặc ngược lại
- Sao lưu lại MBR trong máy (MBR là một file dữ liệu rất quan trong nó chứa các lệnh để LILO hoặc GRUB nạp hệ điều hanh)
- Chuyển đổi chữ thường sang chữ hoa và ngược lại
- Tạo một file với kích cơ cô định
- Tạo một file ISO 

### 4.2.2. Cú pháp và các trường hợp tùy chọn

#### a. Cú pháp
```
#dd if=<địa chỉ đầu vào> of=<địa chỉ đầu ra> option
```
trong đó:

- if=<soure> địa chỉ nguồn của dữ liệu nó sẽ bắt đầu đọc
- of=<targer> viết đầu ra của file
- option : các tùy chọn cho câu lệnh

#### b. Tùy chọn

|Tùy chọn | Ý nghĩa |
|---------|---------|
|bs=Bytes |Quá trình đọc (ghi) bao nhiêu byte một lần đọc (ghi) |
|cbs=Bytes|Chuyển đổi bao nhiêu byte một lần |
|count=Blocks | thực hiện bao nhiêu Block trong quá trình thực thi câu lệnh |
|if | Chỉ đường dẫn đọc đầu vào |
|of | Chỉ đường dẫn ghi đầu ra|
|ibs=bytes | Chỉ ra số byte một lần đọc |
|obs=bytes | Chỉ ra số byte một lần ghi |
|skip=blocks | Bỏ qua bao nhiêu block đầu vào |
|conv=Convs | Chỉ ra tác vụ cụ thể của câu lệnh, các tùy chọn được ghi dưới bảng sau đây |

**Các tùy chọn của conv**

|Tùy chọn | Tác dụng  |
|-----------|----------|
|ascii | Chuyển đôi từ mã EBCDIC sáng ASCII |
|ebcdic | Chuyển đổi từ mã ASCII sang EBCDIC |
|lcase | Chuyển đổi từ chữ thường lên hết thành chữ in hoa |
|ucase | Chuyển đổi từ chữ in hoa sang chữ thường |
|nocreat | Không tạo ra file đầu ra |
|noerror | Tiếp tục sao chép dữ liệu khi đầu vào bị lỗi |
|sync | Đồng bộ dữ liệu với ổ đang sao chép sang |


*Lưu ý:* Khi bạn định dạng số lượng byte mỗi lần đọc. Mặc định nó được tính theo đơn vị là kb. Bạn có thể thêm một số trường sau để báo định dạng khác:

- c = 1 byte
- w = 2 byte
- b = 512 byte
- kB = 1000 byte 
- K = 1024 byte 
- MB = 1000000 byte
- M = (1024 * 1024) byte
- GB = (1000 * 1000 * 1000) byte
- G = (1024 * 1024 * 1024) byte

### 4.2.3. ví dụ hay được sử dụng trong thực thế

##### a. Sao lưu và phục hồi toàn bộ ổ cứng hoặc phân vùng trong ổ cứng

- Sao lưu toàn bộ dữ liệu ổ cứng sao ổ cứng khác:
```
#dd if=/dev/sda of=/dev/sdb conv=noerror,sync
```
Câu lệnh này dùng dể sao lưu toàn bộ dữ liệu của ổ sda sang ổ sdb với tùy chọn trong trường conv=noerrom.sync với ý ngĩa vẫn tiếp tục sao lưu nếu dữ liệu đầu vào bị lỗi và tự động đồng bộ với dữ liệu sdb

- Tạo một file image cho ổ sda1. Các này sẽ nhanh hơn là viêc chuyển dữ liệu sao ổ khác
```
dd if=/dev/sda1 of=/root/sda1.img 
```
- Nếu muốn nén ảnh file anh vào bạn có thể sử dụng command sau
```
dd if=/dev/sda1 | grip > /root/sda1.img.gz
```
-Sao lưu dữ liệu từ một phân vùng này đến một phân vùng khác
```
dd if=/dev/sda2 of=/dev/sdb2 bs=512 conv=noerror,sync
```
*Đối với câu lệnh này bs=512 có ý nghĩa mỗi lần đọc ghi nó đọc và ghi 512 byte*
- Phục hồi dữ liệu 
```
dd if=/root/sda1.img of=/dev/sda1
```
- Sao lưu từ đĩa CDroom
```
dd if=/dev/cdrom of=/root/cdrom.img conv=noerror
```

##### b. Sao lưu phục hồi MBR

Việc sao lưu lại mbr là việc làm cần thiết đối với hệ thống linux. nó đề phòng cho việc khi virut có thể nhảy được hẳn vào vùng MBR. Lúc bày bất kì một phần mềm diệt virut nào cũng không diệt được con virut này. Cách hay nhất là cài đặt lại mbr và lúc đó việc sao chép MBR lúc trước khi nhiễm sẽ phát huy tác dụng:
- Sao chép MBR
```
dd if=/dev/sda1 of=/root/mbr.txt bs=512 count=1
```
- Phục hồi lại MBR
```
dd if=/root/mbr.txt of=/dev/sda1
```

##### c. chuyển đổi chữ thường thành chữ hoa

-  Chuyển chữ thường thành chữ in hoa
```
dd if=/root/test.doc of=/root/test1.doc conv=ucase
```
![](./Images/Report2/46.png)

- Chuyển chứ hoa thành chứ thường
```
dd if=/root/test1.doc of=/test2.doc conv=case,sycn

```
##### d. Tạo một file có dung lượng cố định 
Tạo ra một file có kích thước 100M
```
dd if=/dev/zero of=/home/attt/file1 bs=100M count=1
```
![](./Images/Report2/47.png)

### 4.2.4. Một số tình huống áp dụng trong thực tế

VD1: Kết hợp với câu lệnh mkswap để tạo phân vùng swap cho máy

- Sử dụng câu lênh dd để tạo một phân vùng trống có kích cỡ 512M:
```
dd if=/dev/zero of=/root/swap bs=512M count=1
```
![](./Images/Report2/48.png)

- Gắn quyền cho nó chỉ mới root vào xem đượ
```
chmod 600 /root/swap
```
![](./Images/Report2/49.png)

- chỉ cho đến vùng swap
```
mkswap /root/swap
swapon /root/swap
```
![](./Images/Report2/410.png)

kiểm tra thành công hay chưa. Sử dụng lệnh
```
swapon -s
```
![](./Images/Report2/411.png)

kiểm tra tổng dung lượng vùng swap

![](./Images/Report2/412.png)

Nếu bạn muốn tạo vùng swap không bị mất khi reboot lại máy. Bạn vào file này rồi chỉnh sửa như sau:

	vi /etc/fstab
	rồi chỉnh sửa:
	/root/swap                 swap                    swap                defaults        0  0

VD2: Ngoài ra bạn còn có thể kết hợp với câu lênh crontab để có thể lâp lịch sao chép dữ liêu ổ cứng của bạn theo định kì Đầu tiên vào một file sh để chạy

	vi dd_command.sh
	với nội dung là:
	dd if=/dev/sda1 of=/dev/sdb1 conv=noerror,sync

Tạo một crotab cho file chạy

	crontab 0 10 * * * sh dd_command.sh

Lúc này đến 10h hàng ngày quá trình sao chép dữ liệu giữa ổ sda1 sang ổ sdb1 được thực hiện

[trở về mục lục](#mucluc)

<a name="P43"> </a>
## 4.3. Mirroring data between systems: rsync

### 4.3.1. Khái niệm về

Remote Sync (Rsync) là một công cụ dùng để sao lưu và phục hồi dữ liệu trong Linux. Với câu lệnh rsync bạn có thể sao lưu và đồng bộ dữ liệu remote từ các máy sử dụng hệ điều hành Linux một cách dễ dàng và thuận tiện.

Một số đặc điểm nổi bật khi dùng Rsync

- hiệu quả trong việc sao lưu và đồng bộ file từ 1 hệ thống khác
- hỗ  trợ sao chếp link, devices, owners, groups, permissions
- nhanh hơn sử dụng secure copy
- Rsync tiêu tốn ít bandwidth vì nó sử dụng cơ chế nén khi truyền tải và nhận dử liệu

### 4.3.2. Cú pháp cơ bản trong Rsync
```
rsync options source détination
```
###### Các tùy chọn trong rsync

```
-v: verbose
-r: sao chép dữ  liệu theo cách đệ quy.
-a: chế độ lưu trữ cho phép sao chép các tệp đệ quy và giữ các liên kết, quyề sở hữu, nhóm và mốc thời gian
-z: nén dữ liệu
-h: định dạng số
```

###### Cài đặt Rsync

```
yum install rsync (On Red Hat based systems)
apt-get install rsync (on Debian based systems)
```
![](./Images/Report2/413.png)

######  -  Sao lưu , đồng bộ file trên local 

Để copy file Test1.tar sang thư mục /home/attt/ ta làm như sau

![](./Images/Report2/414.png)

Khi thư mục đích chưa tồn tại thì rsync sẽ tự động tạo thư mục đích cho bạn Sao lưu đồng bộ thư mục trên local.

có thể đồng bộ toàn bộ file trong một thư mục tới 1 thư mục khác trên local, ví dụ bạn muốn dồng bộ thư mục /folder1 tới /folder2/

```
#rsync -avzh /folder1 /folder2/
```
![](./Images/Report2/415.png)

###### -  Sao lưu, đồng bộ dữ liệu từ Server về local và từ local lên Server

- Copy dữ liệu từ local lên server

Sao chép thư mục từ local lên Remote Server

Bạn có 1 thư mục chứa ảnh trên local images/ và bạn muốn đồng bộ lên server có IP x.x.x.x :
```
$ rsync -avz images/ root@x.x.x.x:/home/
```
- Copy dữ liệu từ server về local

Bạn có 1 thư mục chứa ảnh trên server là images/ và bạn muốn đồng bộ về máy local của bạn :

```
# rsync -avzh root@x.x.x.x:/home/images /home/images/
```
###### - Rsync qua SSH

Sử dụng SSH khi truyền tải file để đảm bảo file của bạn được bảo mật và không ai có thể đọc được dữ liệu khi dữ liệu được truyền tải qua internet.

Nên cần cấp quyền user/root mật khẩu để hoàn thành tác vụ. Copy File từ Remote Server về local với SSH

thêm option "-e" khi sử dụng SSH với rsync để truyền tải file.

```
# rsync -avzhe ssh root@x.x.x.x:/root/install.log /tmp/

```
![](./Images/Report2/416.png)

copy File từ Local lên Remote Server với SSH

```
# rsync -avzhe ssh backup.tar root@x.x.x.x:/backups/
```
![](./Images/Report2/416.png)

###### - Hiển thị quá trình truyền dữ liệu khi dùng rsync

Để hiển thị tiến trình truyền dữ liệu ta sử dung ‘–progress’. Nó sẽ hiển thị file và thời gian còn lại cho tới khi hoàn thành truyền dữ liệu.
```
# rsync -avzhe ssh --progress /home/folder root@x.x.x.x:/root/folder
```

###### - Sử dụng -include và -exclude

Sử dụng  2 option này để bạn có thể chỉ định các file cần được sync hoặc bỏ quan không sync

```
# rsync -avze ssh --include 'R*' --exclude '*' root@x.x.x.x:/var/lib/rpm/ /root/rpm
```
###### - Sử dụng -delete

Nếu file hoặc thư mục không tồn tại ở thư mục synx nhưng lại tồn tại ở thư mục đích, bạn cần xóa chúng khi sync, sử dụngk "-delete"
```
# rsync -avz --delete root@x.x.x.x:/var/lib/rpm/
```
##### - Do a Dry Run with rsync
Nếu mới dùng rsync, ta có thể sử dụng "-dry-run" để đảm bảo những tao tác của bạn

```
# rsync --dry-run --remove-source-files -zvh backup.tar /tmp/backups/
```
###### - Cấu hình băng thông cho file truyền tải

Sử dụng ‘–bwlimit‘ để giới hạn bandwidth khi truyền tải file.
```
# rsync --bwlimit=100 -avzhe ssh  /var/lib/rpm/  root@x.x.x.x:/root/tmprpm/

# rsync -zvhW backup.tar /tmp/backups/backup.tar
```

[trở về mục lục](#mucluc)
