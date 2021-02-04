# Cinder

#### [1.Install and configure](#1)



------------------------------------------------------

## 1.Install and configure<a name="1"></a>

#### 1.1. Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database: `CREATE DATABASE cinder;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'localhost' \
    IDENTIFIED BY 'CINDER_DBPASS';` 
    
  - `GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' \
        IDENTIFIED BY 'CINDER_DBPASS';`
  
  - Lưu ý: thay CINDER_DBPASS bằng mật khẩu muốn đặt
  

#### 1.2. Xác thực, ủy quyền với Cinder

- Chạy file client environment scripts **admin-openrc** để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): `. admin-openrc`
- Tạo user *cinder*: `openstack user create --domain default --password-prompt cinder`

- Tạo role phân quyền project *service* cho user cinder: `openstack role add --project service --user cinder admin`

- Tạo service (type volumev2, volumev3)  :  

  ```
  $ openstack service create --name cinderv2 --description "OpenStack Block Storage" volumev2
  $ openstack service create --name cinderv3 --description "OpenStack Block Storage" volumev3
  ```

[Tham khảo các option tạo service](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/service.html)

- Tạo Cinder API endpoints:

  ```
  $ openstack endpoint create --region RegionOne volumev2 public http://controller:8776/v2/%\(project_id\)s
  $ openstack endpoint create --region RegionOne volumev2 internal http://controller:8776/v2/%\(project_id\)s
  $ openstack endpoint create --region RegionOne volumev2 admin http://controller:8776/v2/%\(project_id\)s
  $ openstack endpoint create --region RegionOne volumev3 public http://controller:8776/v3/%\(project_id\)s
  $ openstack endpoint create --region RegionOne volumev3 internal http://controller:8776/v3/%\(project_id\)s
  openstack endpoint create --region RegionOne volumev3 admin http://controller:8776/v3/%\(project_id\)s
  ```

#### 1.3. Cài đặt và cấu hình Cinder

- Install packages : `yum install openstack-cinder-api lvm2 device-mapper-persistent-data openstack-cinder targetcli python-keystone`

- Config: `vi  /etc/cinder/cinder.conf`

  - Input: 

    ```
    [DEFAULT]
    # ...
    transport_url = rabbit://openstack:RABBIT_PASS@controller
    auth_strategy = keystone
    my_ip = [controller_ip]
    enabled_backends = lvm
    glance_api_servers = http://controller:9292
    
    [database]
    # ...
    connection =  mysql+pymysql://cinder:CINDER_DBPASS@controller/cinder
    
    
    [keystone_authtoken]
    # ...
    www_authenticate_uri = http://controller:5000
    auth_url = http://controller:5000
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = Default
    user_domain_name = Default
    project_name = service
    username = cinder
    password = CINDER_PASS
    
    [oslo_concurrency]
    # ...
    lock_path = /var/lib/cinder/tmp
    
    [lvm]
    volume_driver = cinder.volume.drivers.lvm.LVMVolumeDriver
    volume_group = cinder-volumes
    target_protocol = iscsi
    target_helper = lioadm
    ```
    
    Thay CINDER_PASS(password user) và CINDERDB_PASS(password database) bằng mật khẩu đã đặt 

- Tạo bảng cho dịch vụ Image: `su -s /bin/sh -c "cinder-manage db sync" cinder` 

- Config: `vi /etc/nova/nova.conf`. 

  - Input : 

    ```
    [cinder]
    os_region_name = RegionOne
    ```

- Config storage: 

  - Create the LVM physical volume `/dev/sdb`: `pvcreate /dev/sdb`
  - Create the LVM volume group `cinder-volumes`: `vgcreate cinder-volumes /dev/sdb`

- Restart service 
