THIẾT LẬP TƯỜNG LỬA FIREWALLID TRÊN CENTOS7

[1. Các khái niệm cơ bản trong FirewallD](#P1)

[1.1. Zone](#11)

[1.2. Hiệu lực của các quy tắc Runtime/Permanent](#12)

[2. Cài đặt FirewallD](#P2)

[3. Cấu hình FirewallD](#P3)

[3.1. Thiết lập các Zone](#31)

[3.2. Thiết lập các quy tắc](#32)

4. Cấu hình nâng cao

4.1. Tạo Zone riêng

4.2. Định nghĩa services riêng trên FirewallD

5. Kết luận


#  1. các khái niệm cơ bản về firewall <a name="P1"> </a>

## 1.1. Zone <a name="11"> </a>

Trong FirewallD, zone là một nhóm các quy tắc nhằm chỉ ra những luồng dữ liệu được cho phép, dựa trên mức độ tin tưởng của điểm xuất phát luồng dữ liệu đó trong hệ thống mạng. Để sử dụng, bạn có thể lựa chọn zone mặc đinh, thiết lập các quy tắc trong zone hay chỉ định giao diện mạng(Network Interface) để quy định hành vi được cho phép

Các zone được xác định trước theo mức độ tin cậy, theo thứ tự từ “ít-tin-cậy-nhất” đến “đáng-tin-cậy-nhất”:

- drop: ít tin cậy nhất – toàn bộ các kết nối đến sẽ bị từ chối mà không phản hồi, chỉ cho phép duy nhất kết nối đi ra.

- block: tương tự như drop nhưng các kết nối đến bị từ chối và phản hồi bằng tin nhắn từ icmp-host-prohibited (hoặc icmp6-adm-prohibited).

- public: đại diện cho mạng công cộng, không đáng tin cậy. Các máy tính/services khác không được tin tưởng trong hệ thống nhưng vẫn cho phép các kết nối đến trên cơ sở chọn từng trường hợp cụ thể.

- external: hệ thống mạng bên ngoài trong trường hợp bạn sử dụng tường lửa làm gateway, được cấu hình giả lập NAT để giữ bảo mật mạng nội bộ mà vẫn có thể truy cập.

- internal: đối lập với external zone, sử dụng cho phần nội bộ của gateway. Các máy tính/services thuộc zone này thì khá đáng tin cậy.

- dmz: sử dfirewallIDc máy tính/service trong khu vực DMZ(Demilitarized) – cách ly không cho phép truy cập vào phần còn lại của hệ thống mạng, chỉ cho phép một số kết nối đến nhất định.

- work: sử dụng trong công việc, tin tưởng hầu hết các máy tính và một vài services được cho phép hoạt động.

- home: môi trường gia đình – tin tưởng hầu hết các máy tính khác và thêm một vài services được cho phép hoạt động.

- trusted: đáng tin cậy nhất – tin tưởng toàn bộ thiết bị trong hệ thống.


## 1.2. Hiệu lực của các quy tắc Runtime/Permanent <a name="12"> </a>

Trong FirewallD, các quy tắc được cấu hình thời gian hiệu lực Runtime hoặc Permanent.

-  Runtime(mặc định): có tác dụng ngay lập tức, mất hiệu lực khi reboot hệ thống.

- Permanent: không áp dụng cho hệ thống đang chạy, cần reload mới có hiệu lực, tác dụng vĩnh viễn cả khi reboot hệ thống.

Ví dụ, thêm quy tắc cho cả thiết lập Runtime và Permanent:

![](./Images/Firewall/1.1.png)


Việc Restart/Reload sẽ hủy bộ các thiết lập Runtime đồng thời áp dụng thiết lập Permanent mà không hề phá vỡ các kết nối và session hiện tại. Điều này giúp kiểm tra hoạt động của các quy tắc trên tường lửa và dễ dàng khởi động lại nếu có vấn đề xảy ra.


# 2. Cài đặt FirewallD <a name="P2> </a>

- FirewallID được cài đặt mặc định trên Centos7. Nếu chưa có thì sử dụng câu lệnh sau: # yum install firewalld

– Khởi động FirewallD: systemctl start  firewalld

- kiểm tra trạng thái hoạt động : systemctl status firewalld

![](./Images/Firewall/1.2.png)

thiết lập Firewalld khởi động cùng hệ thống

![](./Images/Firewall/1.3.png)

Dừng và vô hiệu hóa 

      #systemctl stop firewalld
      #systemctl disable firewalld
   
 # 3. Cấu hình firewalld <a name="P3"> </a>
 
 ## 3.1. Thiết lập các Zone <a name="31"> </a>
 
 - liệt kê tất cả các zone trong hệ thống : #firewall-cmd --get-zones
 
 ![](./Images/Firewall/1.4.png)

- kiểm tra zone mặc định

![](./Images/Firewall/1.5.png)

- kiểm tra zone active

![](./Images/Firewall/1.6.png)

- thay đổi zone mặc định thành home

![](./Images/Firewall/1.7.png)


## 3.2. Thiết lập các quy tắc <a name="32"> </a>

- liệt kê  toàn bộ caccs quy  tắc của các zones : #firewall-cmd --list-all-zones

![](./Images/Firewall/1.8.png)

- Liệt kê toàn bộ các quy tắc mặc định và zone active

![](./Images/Firewall/1.9.png)










