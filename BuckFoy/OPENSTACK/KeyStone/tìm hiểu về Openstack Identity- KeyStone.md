# Tìm hiểu về keystone

## Mục lục

[1. Tổng quan về keystone](#p1)

## 1. Tổng quan về  keystone

Keystone cung cấp một danh mục các user được ánh xạ tới các dịch vụ openstack để người dùng có thể truy  cập. Openstacj Identity hoạt động như một hệ thống xác thực chung cho toaanf bộ hệ thống và có thể tích hợp với các dịch vụ LPAP,PAM.

KeyStone hỗ trợ  nhiều hình thức xác thực, mật khẩu, hệ thống token, phương thức truy cập AWS

### 1.1 Chức năng

####  1.1.1. Identity 

- nhận diện những người dùng đang truy cập đến các  tài nguyên trên cloud
- trong keystone, identity thường được hiểu là User
- xác  thực về chứng chỉ , dữ liệu của User, Group, Projects, Domain, Roles.

#### 1.1.2. Authentication

- là quá trình xác thực những thông tin để nhận định use
- keystone có tính pluggable tức là nó có thể liên kết với những dịch vụ xác thực người dùng khác như LDAP hoặc Active Directory.
- Thường thì keystone sử dụng Password cho việc xác thực người dùng. Đối với những phần còn lại, keystone sử dụng tokens.
- OpenStack dựa rất nhiều vào tokens để xác thực và keystone chính là dịch vụ duy nhất có thể tạo ra tokens
- Token có giới hạn về thời gian được phép sử dụng. Khi token hết hạn thì user sẽ được cấp một token mới. Cơ chế này làm giảm nguy cơ user bị đánh cắp token.
- Hiện tại, keystone đang sử dụng cơ chế bearer token. Có nghĩa là bất cứ ai có token thì sẽ có khả năng truy cập vào tài nguyên của cloud. Vì vậy việc giữ bí mật token rất quan trọng.

#### 1.1.3. Authorization

- Access Management hay còn được gọi là Authorization là quá trình xác định những tài nguyên mà user được phép truy cập tới.
- Trong OpenStack, keystone kết nối users với những Projects hoặc Domains bằng cách gán role cho user vào những project hoặc domain ấy.
- Các projects trong OpenStack như Nova, Cinder...sẽ kiểm tra mối quan hệ giữa role và các user's project và xác định giá trị của những thông tin này theo cơ chế các quy định (policy engine). Policy engine sẽ tự động kiểm tra các thông tin (thường là role) và xác định xem user được phép thực hiện những gì.

## 1.2. Lợi ích

- Cung cấp giao diện xác thực và quản lí truy cập cho các services của OpenStack. Nó cũng đồng thời lo toàn bộ việc giao tiếp và làm việc với các hệ thống xác thực bên ngoài.
- Cung cấp danh sách đăng kí các containers (“Projects”) mà nhờ vậy các services khác của OpenStack có thể dùng nó để "tách" tài nguyên (servers, images,...)
- Cung cấp danh sách đăng kí các Domains được dùng để định nghĩa các khu vực riêng biệt cho users, groups, và projects khiến các khách hàng trở nên "tách biệt" với nhau.
- Danh sách đăng kí các Roles được keystone dùng để ủy quyền cho các services của OpenStack thông qua file policy.
- Assignment store cho phép users và groups được gán các roles trong projects và domains.
- Catalog lưu trưc các services của OpenStack, endpoints và regions, cho phép người dùng tìm kiếm các endpoints của services mà họ cần.

## 2. KeyStone Concepts

###  2.1. Project

- Trong những ngày đầu của Openstack, có một khái niệm là **Tenants**. Sau đó người ta sử dụng khái niệm Project để thay thế cho trực quan hơn.

- Project là nhóm và cô lập lại các tài nguyên.
- Mục đích: Keystone đăng ký các project và sẽ xác định ai nên được phép truy cập vào những project này.
- Project không phải là của riêng user hay user group. Nhưng user được cho phép truy cập tới project sử dụng khái niệm `Role assignment`.
- Có một Role được gán cho user hoặc user group trên một project. Có nghĩa là user có một vài cách để tiếp cận tới tài nguyên trong project. Và các vai trò cụ thể đã gán cho user sẽ xác định loại quyền truy cập và các khả năng mà user được quyền có.
- Việc gán role cho user đôi khi còn được gọi là "grant" trong Openstack Document.

![image-20201225135559655](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20201225135559655.png)

### 2.2. Domains

- Trong những ngày đầu của Openstack chưa có cơ chế giới hạn khả năng "hiện thị" project của các tổ chức người dùng khác nhau. Điều này có thể gây ra các xung đột không mong muốn đối với tên project của các tổ chức khác nhau.
- Username cũng có tính toàn cụ và cũng có thể dẫn đến xung đột về tên.
- Để openstack có thể hỗ trợ các tổ chức một các rõ ràng trong việc đặt tên, người ta đã sử dụng một khái niệm `Domain`.
- Domain sẽ giới hạn khả năng hiện thị các project và user của các tổ chức.
- Domain là một tập hợp các user, group và project

<img src="C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20201225140346558.png" alt="image-20201225140346558" style="zoom:80%;" />

### 2.3. Users và User Groups

- User Group là nhóm các user
- Chúng ta gọi user và user group là actor

![image-20201225140558178](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20201225140558178.png)