# KeyStone

#### [1.Install and configure](#1)

#### [2.Create a domain, project, users and roles](#2)

#### [3.Verify operation](#3)

#### [4.Create OpenStack client environment scripts](#4)

------------------------------------------------------

## 1.Install and configure<a name="1"></a>

#### 1.1. Tạo database



- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database: `CREATE DATABASE keystone;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'KEYSTONE_DBPASS';` 

  - `GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'KEYSTONE_DBPASS';`

  - Lưu ý: thay KEYSTONE_DBPASS bằng mật khẩu muốn đặt

    ![](../images/OpenStack/KeyStone/db.png)

#### 1.2. Cài đặt và cấu hình KeyStone

- Install packages : `yum install openstack-keystone httpd mod_wsgi`

- Config: `vi  /etc/keystone/keystone.conf`

  - Input: 

    ```
    [database]
    # ...
    connection = mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone
    ...
    [token]
    # ...
    provider = fernet
    ```

- Tạo bảng cho dịch vụ Identity: `su -s /bin/sh -c "keystone-manage db_sync" keystone` - Các bảng sau sẽ tự động được tạo khi chạy lệnh trên

![](../images/OpenStack/KeyStone/tb.png)

- Khởi tạo kho khóa Fernet: 

  - `keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone`
  - `keystone-manage credential_setup --keystone-user keystone --keystone-group keystone`
  - Các tham số --keystone-user và keystone-group chỉ định user/group của hệ điều hành được phép chạy KeyStone. Ở đây user/group được chọn là 'keystone'

- Bootstrap the Identity service:<a name="bootstrap"></a>

  ```
  keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
    --bootstrap-admin-url http://controller:5000/v3/ \
    --bootstrap-internal-url http://controller:5000/v3/ \
    --bootstrap-public-url http://controller:5000/v3/ \
    --bootstrap-region-id RegionOne
  ```

Thay thế ADMIN_PASS bằng mật khẩu muốn đặt

#### 1.3. Cấu hình Apache HTTP server

- Config: `vi /etc/httpd/conf/httpd.conf` => input `ServerName controller` (thay thế ServerName cũ)
- Tạo đường dẫn đến file cấu hình có sẵn */usr/share/keystone/wsgi-keystone.conf*: `ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/` - lệnh này sẽ tạo thêm 1 file cấu hình *wsgi-keystone.conf* tại */etc/httpd/conf.d/*
- Khai báo hosts: `vi /etc/hosts` => input `[IP controller node] controller`

![](../images/OpenStack/KeyStone/hosts.png)

- Restart service
- Cấu hình tài khoản admin:

```
$ export OS_USERNAME=admin
$ export OS_PASSWORD=ADMIN_PASS
$ export OS_PROJECT_NAME=admin
$ export OS_USER_DOMAIN_NAME=Default
$ export OS_PROJECT_DOMAIN_NAME=Default
$ export OS_AUTH_URL=http://controller:5000/v3
$ export OS_IDENTITY_API_VERSION=3
```

Thay ADMIN_PASS bằng password đặt ở [bootstrap](#bootstrap)

- Enable and start service:
  - `systemctl enable httpd.service`
  - `systemctl start httpd.service`

## 2.Create a domain, project, users and roles<a name="2"></a>

> 

- Tạo domain mới (tên example, nội dung "An Example Domain") : `openstack domain create --description "An Example Domain" example`

  ![](../images/OpenStack/KeyStone/crdomain.png)

  [Tham khảo các option tạo domain](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/domain.html)

- Tạo project mới (tên example, nội dung "An Example Project"): `openstack project create --domain default --description "An Example Project" example`

![](../images/OpenStack/KeyStone/crproject.png)

[Tham khảo các option tạo project](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/project.html)

- Tạo user mới (tên myuser, không có password): `openstack user create --domain default --password-prompt myuser` - có thể đặt password sau khi chạy lệnh trên

![](../images/OpenStack/KeyStone/cruser.png)

[Tham khảo các option tạo user](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/user.html)

- Tạo role mới : `openstack role create myrole`

![](../images/OpenStack/KeyStone/crrole.png)

[Tham khảo các option tạo role](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/role.html)

## 3.Verify operation<a name="3"></a>

Cài đặt dịch vụ xác minh danh tính trước khi cài đặt các dịch vụ khác 

- Bỏ các biến tạm thời  OS_AUTH_URL và OS_PASSWORD - các biến chứa thông tin xác thực, nên loại bỏ để KeyStone yêu cầu xác thực mỗi lần truy cập:

  ​	`unset OS_AUTH_URL OS_PASSWORD`

- Yêu cầu token xác thực (với tư cách admin-sử dụng password của user admin và đường dẫn xác thực là đường dẫn đến controller node): 

```
openstack --os-auth-url http://controller:5000/v3 \
  --os-project-domain-name Default --os-user-domain-name Default \
  --os-project-name admin --os-username admin token issue
```

<img src="../images/OpenStack/KeyStone/admintoken.png"  />

- Yêu cầu token xác thực (với các người dùng khác-sử dụng password của user đó và đường dẫn xác thực là đường dẫn đến controller node): 

<img src="../images/OpenStack/KeyStone/usertoken.png"  />

Khi chưa khai báo password, request sẽ bị từ chối với thông báo *The request you have made requires authentication.* (Yêu cầu xác thực). Sau khi khai báo password với biến `--os-password` request token đã được xác thực. 

## 4.Create OpenStack client environment scripts<a name="4"></a>



Các phần trước đã sử dụng kết hợp các biến môi trường và các tùy chọn lệnh để tương tác với dịch vụ Identity thông qua openstack client. Để tăng hiệu quả hoạt động của client, OpenStack hỗ trợ các client environment scripts đơn giản còn được gọi là tệp OpenRC. Các tập scripts này thường chứa các tùy chọn chung cho tất cả các máy khách, nhưng cũng hỗ trợ các tùy chọn duy nhất, ví dụ với người dùng admin:

- Tạo scripts **admin-openrc**: `vi admin-openrc`. Thêm các thông tin xác thực phù hợp vào

  ```
  export OS_PROJECT_DOMAIN_NAME=Default
  export OS_USER_DOMAIN_NAME=Default
  export OS_PROJECT_NAME=admin
  export OS_USERNAME=admin
  export OS_PASSWORD=ADMIN_PASS
  export OS_AUTH_URL=http://controller:5000/v3
  export OS_IDENTITY_API_VERSION=3
  export OS_IMAGE_API_VERSION=2
  ```

Khi chạy client với một project hay user cụ thể, chỉ cần chạy client environment scripts tương ứng đã tạo để xác thực: `. admin-openrc`

Ví dụ:

- File client environment scripts **admin-openrc**

![](../images/OpenStack/KeyStone/vd1.png)

- Bỏ các biến đăng nhập tạm thời  OS_AUTH_URL và OS_PASSWORD: `unset OS_AUTH_URL OS_PASSWORD`. Lúc này khi chạy client sẽ yêu cầu xác thực người dùng, ví dụ với câu lệnh yêu cầu thông báo xác thực

![](../images/OpenStack/KeyStone/vd2.png)

- Chạy file client environment scripts **admin-openrc**: `. admin-openrc`, sau đó yêu cầu thông báo xác thực sẽ cho ra kết quả xác thực

![](../images/OpenStack/KeyStone/vd3.png)