# CONNECTING LINUX TO NETWORK
#### [1.Basic network configuration](#1)
#### [2.IPv4 addressing (dhcp/static)](#2)
#### [3.Network protocols](#3)
#### [4.Network services and port numbers](#4)
#### [5.Managing network devices](#5)
#### [6.Hostnames and DNS](#6)
#### [7.Searching domains](#7)
#### [8.Routing under Linux](#8)
#### [9.Configuring network time](#9)
#### [10.The time zone](#0) 

## 1. Basic network configuration <a name="1"></a>
> Cấu hình mạng(network configuration)(còn được gọi là thiết lập mạng(network setup)) là quá trình thiết lập các điều khiển, luồng và hoạt động của mạng để hỗ trợ giao tiếp mạng của một tổ chức và (hoặc) chủ sở hữu mạng. Thuật ngữ này kết hợp nhiều quá trình cấu hình và thiết lập trên phần cứng, phần mềm mạng và các thiết bị và thành phần hỗ trợ khác.

- Các thông tin cần thiết cho quá trình cấu hình mạng:
  - Tên đầy đủ của máy tính
  - Địa chỉ IP, địa chỉ mac của máy
  - Mặt nạ mạng ( subnet mask) (nếu có)
  - Địa chỉ quảng bá (broadcast address) 
  
 - Quá trình bao gồm các nhiệm vụ sau(các bước cơ bản) : 
  - Cấu hình bộ định tuyến: chỉ định địa chỉ ip chính xác hoặc để bộ định tuyến tạo tự động, cài đặt route v.v.  Có thể thực hiện theo hướng dẫn của hãng sản xuất router: Kết nối cáp mạng -> thiết lập chế độ cho router -> thiết lập các tùy chọn theo nhu cầu/cách thức sử dụng)
  - Cấu hình máy chủ: Thiết lập kết nối mạng trên máy tính bằng cách ghi lại cài đặt mạng như địa chỉ IP, proxy, tên mạng, ID/password để kích hoạt kết nối mạng và giao tiếp: Đăng nhập vào mạng thông qua id/password của router(nếu có) -> cài đặt địa chỉ ip, proxy, dns v.v.
  - Cấu hình phần mềm: Thiết lập các phần mềm sử dụng kết nối: thiết lập theo yêu cầu của từng phần mềm
  - Các cấu hình khác như internet, network sharing, firewall v.v.
 

## 2. IPv4 addressing (dhcp/static) <a name="2"></a>
> Giao thức Internet phiên bản 4 (IPv4/Internet Protocol version 4) là phiên bản thứ tư trong quá trình phát triển của các giao thức Internet (IP). Đây là phiên bản đầu tiên của IP được sử dụng rộng rãi. IPv4 cùng với IPv6 là nòng cốt của giao tiếp internet. Hiện tại, IPv4 vẫn là giao thức được triển khai rộng rãi nhất trong bộ giao thức của lớp internet.

> IPv4 có chiều dài 32 bit để đánh địa chỉ, theo đó, số địa chỉ tối đa có thể sử dụng là 4.294.967.296 (2^32). Tuy nhiên, do một số được sử dụng cho các mục đích khác như: cấp cho mạng cá nhân (xấp xỉ 18 triệu địa chỉ), hoặc sử dụng làm địa chỉ quảng bá (xấp xỉ 16 triệu), nên số lượng địa chỉ thực tế có thể sử dụng cho mạng Internet công cộng bị giảm xuống. Với sự phát triển không ngừng của mạng Internet, nguy cơ thiếu hụt địa chỉ đã được dự báo, tuy nhiên, nhờ công nghệ NAT (Network Address Translation) tạo nên hai vùng mạng riêng biệt: Mạng riêng và Mạng công cộng, địa chỉ mạng sử dụng ở mạng riêng có thể dùng lại ở mạng công công mà không hề bị xung đột, qua đó trì hoãn được vấn đề thiếu hụt địa chỉ.

- Configuring Static IP address using DHCP:
Dynamic Host Configuration Protocol (DHCP) là một giao thức cho phép cấp phát địa chỉ IP một cách tự động cùng với subnet mask và gateway mặc định. Máy tính được cấu hình một cách tự động vì thế sẽ giảm việc can thiệp vào hệ thống mạng, mục đích quan trọng nhất là tránh trường hợp hai máy tính khác nhau lại có cùng địa chỉ IP. DHCP thường là cài đặt mặc định có sẵn, nếu không, các bước để định cấu hình đặt trước DHCP khác nhau giữa các bộ định tuyến và có thể tham khảo tài liệu của nhà cung cấp.

- Configuring Static IP address on Ubuntu:
Các bước cấu hình ip một cách thủ công trong hệ thống ubuntu:
  - B1: Kiểm tra tên của ethernet interface, gõ `ip link`, hệ thống sẽ in ra danh sách các ethernet interface có sẵn, trong đa số các trường hợp sẽ có 2 ethernet interface: lo - giao diện loopback được hệ thống sử dụng để giao tiếp với chính nó - và 1(hoặc nhiều) ethernet interface được sử dụng để kết nối ethernet - trong trường hợp này là ens38
  
  > ![](./images/report3/iplink.png)
  
  - B2: Mở và tiến hành cấu hình thủ công ethernet interface với file config:
    > Với các hệ thống ubuntu 17.10 trở lên, netplan là công cụ quản lý mạng mặc định. Để chỉnh sửa file config: `vi /etc/netplan/01-network-manager-all.yaml`
    
    > ![](./images/report3/dfnetplan.png)
    
    Với hệ thống trên, 3 từ khóa network, version và renderer là 3 thuộc tính mặc định. Tiếp theo, ethernets là loại thiết bị được cấu hình (loại thiết bị có thể là ethernets, bonds, bridges, hoặc vlans. Với mỗi loại thiết bị có thể chỉ định nhiều ethernet interface, ở đây có 1 giao diện là ens38 được cấu hình để lấy ip từ máy chủ dhcp: `dhcp4: yes`
    
    Để cấu hình ip tĩnh:
    
    > ![](./images/report3/cfnetplan.png)
    
    - dhcp4: no : k sử dụng ip từ máy chủ dhcp
    - addresses : địa chỉ ip tĩnh đặt cho ethernet interface, không được trùng với các ip của máy khác.
    - gateway : cổng cho phép kết nối đến internet
    - nameserver : ip của máy chủ định danh 
    
    //error
    
    > Hoặc sử dụng ifconfig cho mọi hệ thống ubuntu: `vi /etc/network/interfaces`
    
    > ![](./images/report3/dfif.png)
    
    - auto: interface sẽ được cấu hình khi khởi động
    - iface: interface
    - inet: sử dụng TCP/IP networking
    
    Để cấu hình ip tĩnh:
    
    > ![](./images/report3/cfif.png)
    
    - inet static: sử dụng địa chỉ ip tĩnh
    - address: địa chỉ ip tĩnh đặt cho ethernet interface, không được trùng với các ip của máy khác.
    - gateway : cổng cho phép kết nối đến internet
    - netmask, broadcast: 
    - dns-nameserver : ip của máy chủ định danh 

## 3. Network protocols<a name="3"></a>
#### 3.1. Network protocol
> Giao thức mạng(Network protocols) là một tập hợp các quy tắc được thiết lập để xác định cách dữ liệu được truyền giữa các thiết bị khác nhau trong cùng một mạng. Về cơ bản, nó cho phép các thiết bị được kết nối giao tiếp với nhau, bất kể bất kỳ sự khác biệt nào về quy trình, cấu trúc(phần cứng) hoặc thiết kế nội bộ của chúng.

Các giao thức mạng có ba hành động chính: giao tiếp(Communication), quản lý (Network management) và bảo mật(Security)
- Communication: Các giao thức truyền thông cho phép các thiết bị mạng khác nhau giao tiếp với nhau. Các loại giao thức truyền thông phổ biến bao gồm:
  - Tự động hóa : Các giao thức này được sử dụng để tự động hóa các quy trình khác nhau trong cả cài đặt thương mại và cá nhân, chẳng hạn như trong các tòa nhà thông minh, công nghệ đám mây hoặc xe tự lái.
  - Định tuyến(routing): phương thức giao tiếp giữa bộ định tuyến và các thiết bị mạng khác
  - Internet Protocol(IP): cho phép dữ liệu chuyển đi giữa các thiết bị thông qua internet
  - Bluetooth, transfer, v.v.
- Network management: Các giao thức quản lý mạng xác định và mô tả các thủ tục khác nhau cần thiết để vận hành hiệu quả một mạng máy tính. Các giao thức này ảnh hưởng đến các thiết bị khác nhau trên một mạng - bao gồm bộ định tuyến và máy chủ và các thiết bị kết nối đến mạng - để đảm bảo mỗi thiết bị và toàn bộ mạng hoạt động tối ưu. Gồm các chức năng:
  - Kết nối : Các giao thức này thiết lập và duy trì kết nối ổn định giữa các thiết bị khác nhau trên cùng một mạng.
  - Tổng hợp liên kết : Các giao thức tổng hợp liên kết cho phép bạn kết hợp nhiều kết nối mạng thành một liên kết giữa hai thiết bị. Điều này có tác dụng tăng cường độ bền của kết nối và giúp duy trì kết nối nếu một trong các liên kết bị lỗi.
  - Khắc phục sự cố : Các giao thức khắc phục sự cố cho phép quản trị viên mạng xác định các lỗi ảnh hưởng đến mạng, đánh giá chất lượng kết nối mạng và xác định cách quản trị viên có thể khắc phục mọi sự cố.
- Security: Các giao thức bảo mật hoạt động để đảm bảo rằng mạng và dữ liệu được gửi qua nó được bảo vệ khỏi những người dùng trái phép. Các chức năng gồm:
  - Mã hóa
  - Xác thực danh tính
  
  
#### 3.2. Mô hình TCP/IP
> TCP/IP (Transmission Control Protocol và Internet Protocol- giao thức điều khiển giao vận dữ liệu và giao thức kết nối internet) là giao thức mà hầu hết các mạng máy tính ngày nay đều sử dụng để kết nối. Cơ chế hoạt động của mô hình này là IP đóng vai trò kết nối, truyền dữ liệu giữa các thiết bị kết nối internet và TCP kiểm soát dữ liệu được truyền đi đó, đảm bảo rằng dữ liệu được truyền đi 1 cách đầy đủ, toàn vẹn

> ![](./images/report3/np.png)

Mô hình TCP/IP tiêu chuẩn bao gồm 4 tầng được chồng lên nhau, bắt đầu từ tầng thấp nhất là:
- Tầng vật lý (Physical) : chịu trách nhiệm truyền dữ liệu giữa hai thiết bị trong cùng một mạng. Tại đây, các gói dữ liệu được đóng vào khung (gọi là Frame) và được định tuyến đi đến đích đã được chỉ định ban đầu.
- Tầng 2: Tầng mạng (Network, Internet): đảm nhận việc truyền tải dữ liệu một cách hợp lý. Các giao thức bao gồm IP (Internet Protocol), ICMP (Internet Control Message Protocol), IGMP (Internet Group Message Protocol).
- Tầng 3: Tầng giao vận (Transport): hoạt động thông qua hai giao thức chính là TCP (Transmisson Control Protocol) và UDP (User Datagram Protocol). TCP sẽ đảm bảo chất lượng truyền gửi gói tin, tuy nhiên lại mất thời gian khá lâu để thực hiện các thủ tục kiếm soát dữ liệu. Ngược lại, UDP lại cho tốc độ truyền tải nhanh nhưng lại không đảm bảo được chất lượng dữ liệu. Ở tầng này, TCP và UDP sẽ hỗ trợ nhau phân luồng dữ liệu.
- Tầng 4: Tầng ứng dụng (Application): đảm nhận vai trò giao tiếp dữ liệu giữa 2 máy khác nhau thông qua các dịch vụ mạng khác nhau (duyệt web, các giao thức trao đổi dữ liệu SMTP, SSH, FTP, HTTP…).

 #### 3.3. Mô hình OSI
 > Mô hình OSI (Open Systems Interconnection Reference Model)còn được gọi với cái tên: mô hình kết nối hệ thống mở. Mô hình này chia giao tiếp mạng thành 7 lớp. Trong đó, lớp 1 đến 4 là những cấp thấp và chỉ thực hiện nhiệm vụ truyền tải dữ liệu. Lớp 5 đến lớp 7 sẽ là lớp cấp cao, có nhiệm vụ đặc phù, xử lý các vấn đề ứng dụng và tham gia vào chuỗi mắt xích truyền tải dữ liệu đến những lớp tiếp theo.
## 5. Managing network devices<a name="5"></a>
## 6. Hostnames and DNS<a name="6"></a>
## 7. Searching domains<a name="7"></a>
## 8. Routing under Linux<a name="8"></a>
## 9. Configuring network time<a name="9"></a>
## 10. The time zone <a name="0"></a>
