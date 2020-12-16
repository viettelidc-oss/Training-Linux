# [1. Rsyslog/syslog configuration](#1)
# [2. Testing using logger](#2)
# [3. Managing logs with logrotate](#3)
# [4. The systemd journal: journalctl](#4) 



##  1. Rsyslog/syslog configuration <a name="1"></a>

#### 1.1. Syslog 

> ​	Syslog là một giao thức client/server dùng để gửi log và message đến máy nhận log. Máy nhận log thường được gọi là syslogd, syslog daemon hoặc syslog server. Syslog có thể gửi qua UDP hoặc TCP. Các dữ liệu được gửi dạng cleartext. Syslog dùng port 514 cho UDP và port 10514 cho TCP.

Giao thức **syslog** có những yếu tố sau:

- **Defining an architecture** : Syslog là một giao thức, là một phần của kiến trúc mạng hoàn chỉnh, với nhiều máy khách và máy chủ.
- **Message format**: syslog xác định cách định dạng tin nhắn. Điều này rõ ràng cần phải được chuẩn hóa vì các bản ghi thường được phân tích cú pháp và lưu trữ vào các công cụ lưu trữ khác nhau. Do đó, chúng ta cần xác định những gì một máy khách syslog có thể tạo ra và những gì một máy chủ có thể nhận được.
- **Specifying reliability** : syslog cần xác định cách xử lý các tin nhắn không thể gửi được. Là một phần của TCP/IP, syslog rõ ràng sẽ bị thay đổi trên giao thức mạng cơ bản (TCP hoặc UDP) để lựa chọn.
- **Dealing with authentication or message authenticity** : syslog cần một cách đáng tin cậy để đảm bảo rằng máy khách và máy chủ đang nói chuyện một cách an toàn và tin nhắn nhận được không bị thay đổi.

>  Hiện nay, rsyslog được sử dụng thay thế cho syslog trên các hệ thống hiện đại. Rsyslog là một sự phát triển của syslog, cung cấp các khả năng như các mô đun có thể cấu hình, được liên kết với nhiều mục tiêu khác nhau. 

#### 1.2. Syslog message format 

Theo mặc định, Syslog message format được chia thành hai phần, độ dài một message không được vượt quá 1024 bytes

>  ![](./images/report3/syslog.png)



Để tùy chỉnh Syslog message format, mở file rsyslog.conf : `vi /etc/rsyslog.conf`

Khởi tạo template có tên precise, sau đó đặt precise làm template mặc định. 



>  ![](./images/report3/templatelog.png)



Ở đây template có dạng: ` $template precise, "{%syslogpriority%,%syslogfacility%} %timegenerated% %HOSTNAME% %syslogtag% %msg%\n" `. Trong đó:

	- {%syslogpriority%,%syslogfacility%} là mức độ ưu tiên (priority levels: facility levels and severity levels)
	- %timegenerated% %HOSTNAME% là phần Header, gồm timestamp và hostname
	- %syslogtag% %msg% là phần chứ nội dung của log, gồm tag và message

Sau khi thay đổi, Syslog message format trả ra kết quả:



>  ![](./images/report3/syslog1.png)



#### 1.3. Priority levels

- Facility levels là mức độ cơ sở, được sử dụng để xác định chương trình hoặc phần nào của hệ thống tạo ra các bản ghi. 

  - Theo mặc định, một số phần trong hệ thống của bạn được cung cấp các mức facility như kernel sử dụng kern facility hoặc hệ thống mail của bạn sử dụng mail facility.

  - Nếu một bên thứ ba muốn phát hành log, có thể đó sẽ là một tập hợp các cấp độ facility từ 16 đến 23 được gọi là “local use” facility levels.

  - Ngoài ra, họ có thể sử dụng tiện ích của người dùng cấp độ người dùng (“user-level” facility), nghĩa là họ sẽ đưa ra các log liên quan đến người dùng đã ban hành các lệnh.

    

    >  ![](./images/report3/facility.png)

    

- Severity levels là mức độ cảnh báo của Syslog, thể hiện mức độ nghiêm trọng của log event và chúng bao gồm từ gỡ lỗi (debug), thông báo thông tin (informational messages) đến mức khẩn cấp (emergency levels), được chia thành các loại số từ 0 đến 7, 0 là cấp độ khẩn cấp quan trọng nhất



> ![](./images/report3/severity.png)



#### 1.4. Syslog message delivery

Hầu hết thời gian, quản trị viên hệ thống không giám sát một máy duy nhất mà họ phải giám sát rất nhiều máy trong hệ thống. Vậy nên cần phải tập trung syslog vào một máy chủ để dễ dàng theo dõi và quản lý. Có thể sử dụng giao thức TLS/SSL trên TCP để mã hóa các gói Syslog khi gửi đi. 

#### Cấu hình server: `vi /etc/rsyslog.conf` 

- Load các module cần thiết:  

  - `module(load="imuxsock")` : module xử lý Unix Socket Input, quản lý việc phân phối các lệnh gọi syslog,  theo dõi các log sockets của hệ thống Unix và cung cấp cho rsyslog các thông báo nhật ký khi chúng xảy ra. Đây là module được sử dụng nhiều nhất (và bắt buộc, nếu thiếu sẽ k có log nào được ghi) nên thường mặc định có sẵn

  - `module(load="imjournal")` : module cung cấp dữ liệu **có cấu trúc**, nếu k cần cấu trúc dữ liệu thì k cần thiết

  - `module(load="imklog" permitnonkernelfacility="on")` : cung cấp kernel log

  - `module(load="imtcp")`, `input(type="imtcp" port="514")` : cung cấp khả năng nhận syslog qua giao thức tcp

- Cấu hình tùy chỉnh: 
  - `$template RemoteLogs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"` : chỉ định nơi lưu log nhận được, mỗi máy client gửi đến sẽ được lưu vào folder riêng %HOSTNAME%, RemoteLogs là tên của template
  - `*.* ?RemoteLogs`:  cú pháp tổng quát là`[Facility levels].[Severity levels]?[Template]`. Hai dấu * ở đây có ý nghĩa máy chủ sẽ nhận tất cả các log, bất kể mức độ ưu tiên.
  - `&~`: Kí tự chỉ định rsyslog dừng xử lý thông báo sau khi nó được ghi vào tệp.



>  ![](./images/report3/syslogsv.png)



- Mở port 514 (mặc định) hoặc port được cài đặt trong cấu hình, sau đó khởi động lại rsyslog

#### Cấu hình client: `vi /etc/rsyslog.conf`

- Chỉ định máy chủ nhận log: `[Facility levels].[Severity levels]@@[IP]:[port]` , ví dụ:`*.*@@192.168.142.131:514`
- Khởi động lại rsyslog

>  ![](./images/report3/syslogcl.png)

Mô hình: 2 máy client gửi syslog về server. Xuất hiện 2 folder có tên là hostname của 2 máy client chứa các file log nhận được 



>  ![](./images/report3/syslog3.png)



Mở file systemd.log của client "web":



> ![](./images/report3/syslog4.png)



Sau đó thực hiện 1 hành động từ client (ví dụ restart lại service ssh), log về hành động đó sẽ được gửi sang server ngay lập tức



> ![](./images/report3/syslog5.png)