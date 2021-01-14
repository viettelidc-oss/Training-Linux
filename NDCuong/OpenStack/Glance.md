# Glance



#### [1.Install and configure](#1)

#### [2.Verify operation](#2)



------------------------------------------------------

## 1.Install and configure<a name="1"></a>

#### 1.1. Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database: `CREATE DATABASE glance;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'GLANCE_DBPASS';` 

  - `GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'GLANCE_DBPASS';`

  - Lưu ý: thay GLANCE_DBPASS bằng mật khẩu muốn đặt

    ![](../images/OpenStack/Glance/db.png)

#### 1.2. Xác thực, ủy quyền với KeyStone

- Chạy file client environment scripts **admin-openrc** để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): `. admin-openrc`
- Tạo user *glance*: `openstack user create --domain default --password-prompt glance`

![](../images/OpenStack/Glance/nu.png)

- Tạo role phân quyền project *service* (tạo mới nếu chưa có: `openstack project create --domain default  --description "Service Project" service`) cho user *glance*: `openstack role add --project service --user glance admin`
- Tạo service (type image) *glance* :  `openstack service create --name glance --description "OpenStack Image" image`

![](../images/OpenStack/Glance/ns.png)

[Tham khảo các option tạo service](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/service.html)

> Glance quản lý các image, service type image này là đối tượng chính của Glance

- Tạo API endpoints(vị trí mà các API tương tác với hệ thống, truy cập tài nguyên. [Tham khảo](https://smartbear.com/learn/performance-monitoring/api-endpoints/)):

  - endpoint public: `openstack endpoint create --region RegionOne image public http://controller:9292`
  - endpoint internal: `openstack endpoint create --region RegionOne image internal http://controller:9292`
  - endpoint admin: `openstack endpoint create --region RegionOne image admin http://controller:9292`

  ![](../images/OpenStack/Glance/ep.png)

  

#### 1.3. Cài đặt và cấu hình Glance

- Install packages : `yum install openstack-glance`

- Config: `vi  /etc/glance/glance-api.conf`

  - Input: 

    ```
    [database]
    # ...
    connection = mysql+pymysql://glance:GLANCE_DBPASS@controller/glance
    
    [keystone_authtoken]
    # ...
    www_authenticate_uri  = http://controller:5000
    auth_url = http://controller:5000
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = Default
    user_domain_name = Default
    project_name = service
    username = glance
    password = GLANCE_PASS
    
    [paste_deploy]
    # ...
    flavor = keystone
    
    [glance_store]
    # ...
    stores = file,http
    default_store = file
    filesystem_store_datadir = /var/lib/glance/images/
    ```
    
    Thay GLANCE_PASS(password user) và GLANCEDB_PASS(password database) bằng mật khẩu đã đặt 

- Tạo bảng cho dịch vụ Image: `su -s /bin/sh -c "glance-manage db_sync" glance` - Các bảng sau sẽ tự động được tạo khi chạy lệnh trên

![](../images/OpenStack/Glance/tb.png)

- Enable and start service
- ` systemctl enable openstack-glance-api.service`
  - ` systemctl start openstack-glance-api.service`

## 2.Verify operation<a name="2"></a>

- Dowload source image: `wget http://download.cirros-cloud.net/0.4.0/cirros-0.4.0-x86_64-disk.img`
- Upload the image to the Image service using the [QCOW2](https://docs.openstack.org/glance/train/glossary.html#term-qemu-copy-on-write-2-qcow2) disk format, [bare](https://docs.openstack.org/glance/train/glossary.html#term-bare) container format, and public visibility so all projects can access it:`glance image-create --name "cirros" --file cirros-0.4.0-x86_64-disk.img --disk-format qcow2 --container-format bare --visibility public`

![](../images/OpenStack/Glance/ni.png)

- Confirm upload of the image and validate attributes: ` openstack image list`

  ![](../images/OpenStack/Glance/il.png)

