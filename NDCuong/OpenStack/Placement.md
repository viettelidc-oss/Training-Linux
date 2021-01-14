# Placement

#### [1.Install and configure](#1)



------------------------------------------------------

## 1.Install and configure<a name="1"></a>

#### 1.1. Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database: `CREATE DATABASE placement;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' \
    IDENTIFIED BY 'PLACEMENT_DBPASS';` 
    
  - `GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' \
        IDENTIFIED BY 'PLACEMENT_DBPASS';`
  
  - Lưu ý: thay PLACEMENT_DBPASS bằng mật khẩu muốn đặt
  
    ![](../images/OpenStack/Placement/db.png)

#### 1.2. Xác thực, ủy quyền với KeyStone

- Chạy file client environment scripts **admin-openrc** để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): `. admin-openrc`
- Tạo user *placement*: `openstack user create --domain default --password-prompt placement`

![](../images/OpenStack/Placement/nu.png)

- Tạo role phân quyền project *service* cho user *placement*: `openstack role add --project service --user placement admin`
- Tạo service (type placement) *placement* :  `openstack service create --name placement --description "Placement API" placement`

![](../images/OpenStack/Placement/ns.png)

[Tham khảo các option tạo service](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/service.html)

- Tạo Placement API endpoints:

  - endpoint public: `openstack endpoint create --region RegionOne placement public http://controller:8778`
  - endpoint internal: `openstack endpoint create --region RegionOne placement internal http://controller:8778`
  - endpoint admin: `openstack endpoint create --region RegionOne placement admin http://controller:8778`

  ![](../images/OpenStack/Placement/ep.png)

  

#### 1.3. Cài đặt và cấu hình Placement

- Install packages : `yum install openstack-placement-api`

- Config: `vi  /etc/placement/placement.conf`

  - Input: 

    ```
    [placement_database]
    # ...
    connection =  mysql+pymysql://placement:PLACEMENT_DBPASS@controller/placement
    
    [api]
    # ...
    auth_strategy = keystone
    
    [keystone_authtoken]
    # ...
    auth_url = http://controller:5000/v3
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = Default
    user_domain_name = Default
    project_name = service
    username = placement
    password = PLACEMENT_PASS
    ```
    
    Thay PLACEMENT_PASS(password user) và PLACEMENT_DBPASS(password database) bằng mật khẩu đã đặt 

- Tạo bảng cho dịch vụ Image: `su -s /bin/sh -c "placement-manage db sync" placement` - Các bảng sau sẽ tự động được tạo khi chạy lệnh trên

![](../images/OpenStack/Placement/tb.png)

- Restart httpd service:  `systemctl restart httpd`
