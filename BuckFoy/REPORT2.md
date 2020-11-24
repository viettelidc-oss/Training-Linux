#Report 2

<a name="mucluc"> </a>

[muc lục ]

[1.  SYSTEM STARTUP AND SHUTDOWN](#P1)

[1.1. System startup process ](#P11)

[1.2. The startup script framework](#P12)

[1.3. Managing services using](#P13)

[1.5. Managing services with system/service/systemctl)(#P15)

[1.6 Shutdowns and rc](#P16)

<a name="P1"> </a>
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

#### Bước 1.	BIOS

BIOS là chương trình chạy đầu tiên khi nhấn nút nguồn hoặc reset lại máy tính.

BIOS thực hiện một công việc gọi là POST (power on self test) kiểm tra các thông số phần cứng của máy tính. Ngoài ra, BIOS cho phép thay đổi cấu hình, thiết lập nó.
BIOS được lưu trữ trên ROM của bo mạch chủ.

Quá trình POST kết thúc thành công, BIOS sẽ tìm kiếm và   khởi  chạy một hệ điều hành trong các thiết bị lưu trữ như ổ cứng . ..

Hệ điều hành Linux được cài trên ổ cứng thì BIOS sẽ tìm kiếm đến MBR.

#### Bước 2. Master Boot Record
Sau khi BIOS xác định được thiết bị lưu trữ thì BIOS sẽ đọc trong MBR hoặc phân vùng EFI của thiết bị để nạp vào bộ nhớ  một chương trình. Chương trình này sẽ định vị và khởi động boot loader.
	
Đến giai đoạn này, máy tính sẽ không truy cập vào phương tiện lưu trữ. Thông tin về ngày tháng, thời gian và các thiết bị quan trọng nhất được nạp từ CMOS

![hinh11](https://user-images.githubusercontent.com/74639473/100110342-69860280-2e9f-11eb-91aa-468d7f0e9291.png)

#### Bước 3. Boot Loader

Linux có 2 boot loader phổ biến trên Linux là GRUB và ISOLINUX.

Mục đích là cho phép lựa chọn hệ điều hành có trên máy tính để khởi động, sau đó chúng sẽ nạp kernel của hệ điều hành vào bộ nhớ và chuyển quyền điều khiển máy tính cho kernel này.

Ví dụ file cấu hình grub.cfg: “/boot/grub/grub.conf ”

![hinh12](https://user-images.githubusercontent.com/74639473/100110366-6e4ab680-2e9f-11eb-897c-cfbf8cbafd3b.png)

Hệ thống sử dụng BIOS/MBR , bộ tải khởi động nằm ở khu vực đầu tiên của đĩa cứng. Kích thước của MBR chỉ là 512 byte. Bộ nạp khởi  động kiểm tra bảng phân vùng và tìm một phân vùng có khả năng khởi động. Nó tìm thấy 1 phân vùng có khả năng khởi động, nó sẽ tìm kiếm bộ tải khởi động giai đoạn thứ 2.

Với hệ thống sử dụng EFI / UEFI, phần mềm UEFI đọc dữ liệu trình quản lý khởi động để xác định ứng dụng UEFI nào sẽ được khởi chạy và từ nơi nào.

Trình khởi động giai đoạn 2 nằm trong /boot. Hiển thị cho chúng ta chọn hệ điều hành để khởi động. Tiếp đến bộ nạp khởi động sẽ tải hệ điều hành vào RAM và chuyển quyền kiểm soát cho RAM.

![hinh13](https://user-images.githubusercontent.com/74639473/100110375-70147a00-2e9f-11eb-8c81-0b3d7367f3f7.png)

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






# 2. PROCESS MONITORING AND SCHEDULING

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

