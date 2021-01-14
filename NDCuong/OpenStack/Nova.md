# Nova

​	OpenStack Compute là một phần chính của hệ thống Cơ sở hạ tầng dưới dạng dịch vụ (IaaS), sử dụng để lưu trữ và quản lý hệ thống điện toán đám mây. Các mô-đun chính được triển khai bằng Python. 

> ​	OpenStack Compute tương tác với OpenStack Identity để xác thực, OpenStack Placement để theo dõi và lựa chọn kho tài nguyên, dịch vụ OpenStack Image cho hình ảnh đĩa và máy chủ, và OpenStack Dashboard cho giao diện quản trị và người dùng.

[Tham khảo](https://docs.openstack.org/nova/train/install/get-started-compute.html)

#### [1.Install and configure](#1)

#### [2.Verify operation](#2)



------------------------------------------------------

## 1.Install and configure<a name="1"></a>

Trong bài viết này controller node và compute node sẽ được cài đặt trên cùng một máy tính: Linux CentOS 7, ip: 192.168.69.105

#### 1.1. Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database:

  -  `CREATE DATABASE nova_api;`
  - `CREATE DATABASE nova;`
  - `CREATE DATABASE nova_cell0;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' \
    IDENTIFIED BY 'NOVA_DBPASS';` 
    
  - `GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' \
        IDENTIFIED BY 'NOVA_DBPASS';`
  
  - `GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \
      IDENTIFIED BY 'NOVA_DBPASS';` 
  
  - `GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \
      IDENTIFIED BY 'NOVA_DBPASS';`
  
  - `GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' \
      IDENTIFIED BY 'NOVA_DBPASS';` 
  
  - `GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' \
      IDENTIFIED BY 'NOVA_DBPASS';`
  
  - Lưu ý: thay GLANCE_DBPASS bằng mật khẩu muốn đặt
  
    ![](../images/OpenStack/Nova/db.png)

#### 1.2. Xác thực, ủy quyền với KeyStone

- Chạy file client environment scripts **admin-openrc** để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): `. admin-openrc`
- Tạo user *nova*: `openstack user create --domain default --password-prompt nova`

![](../images/OpenStack/Nova/nu.png)

- Tạo role phân quyền project *service* cho user *nova*: `openstack role add --project service --user nova admin`
- Tạo service (type compute) *nova* :  `openstack service create --name nova --description "OpenStack Compute" compute`

![](../images/OpenStack/Nova/ns.png)

[Tham khảo các option tạo service](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/service.html)

- Tạo API endpoints(vị trí mà các API tương tác với hệ thống, truy cập tài nguyên. [Tham khảo](https://smartbear.com/learn/performance-monitoring/api-endpoints/)):

  - endpoint public: `openstack endpoint create --region RegionOne compute public http://controller:8774/v2.1`
  - endpoint internal: `openstack endpoint create --region RegionOne compute internal http://controller:8774/v2.1`
  - endpoint admin: `openstack endpoint create --region RegionOne compute admin http://controller:8774/v2.1`

  ![](../images/OpenStack/Nova/ep.png)

  

#### 1.3. Cài đặt và cấu hình Nova

- Install packages :` yum install openstack-nova-api openstack-nova-conductor openstack-nova-novncproxy openstack-nova-scheduler openstack-nova-compute`

- Config: `vi  /etc/nova/nova.conf`

  - Input: 

    ```
    [DEFAULT]
    # ...
    enabled_apis = osapi_compute,metadata
    transport_url = rabbit://openstack:RABBIT_PASS@controller:5672/
    my_ip = 192.168.69.105
    use_neutron = true
    firewall_driver = nova.virt.firewall.NoopFirewallDriver
    
    [api_database]
    # ...
    connection = mysql+pymysql://nova:NOVA_DBPASS@controller/nova_api
    
    [database]
    # ...
    connection = mysql+pymysql://nova:NOVA_DBPASS@controller/nova
    
    [api]
    # ...
    auth_strategy = keystone
    
    [keystone_authtoken]
    # ...
    www_authenticate_uri = http://controller:5000/
    auth_url = http://controller:5000/
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = Default
    user_domain_name = Default
    project_name = service
    username = nova
    password = NOVA_PASS
    
    [vnc]
    enabled = true
    # ...
    server_listen = $my_ip
    server_proxyclient_address = $my_ip
    novncproxy_base_url = http://controller:6080/vnc_auto.html
    
    [glance]
    # ...
    api_servers = http://controller:9292
    
    [oslo_concurrency]
    # ...
    lock_path = /var/lib/nova/tmp
    
    [placement]
    # ...
    region_name = RegionOne
    project_domain_name = Default
    project_name = service
    auth_type = password
    user_domain_name = Default
    auth_url = http://controller:5000/v3
    username = placement
    password = PLACEMENT_PASS
    ```
    
    Thay các biến *_PASS bằng các mật khẩu tương ứng 
    
    ​	Kiểm tra phần cứng compute node: `egrep -c '(vmx|svm)' /proc/cpuinfo`, nếu kết quả trả về 0 tức là phần cứng compute node k hỗ trợ KVM, cần phải cấu hình *libvirt* sử dụng QEMU: thêm vào file config (/etc/nova/nova.conf) :
    
    ```
    [libvirt]
    # ...
    virt_type = qemu
    ```

- Cấu hình database:

  -  Populate the *nova-api* database: `su -s /bin/sh -c "nova-manage api_db sync" nova`  
  - Register the *cell0* database: `su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova`
  - Create the *cell1* cell:`su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova`
  - Populate the *nova* database:`su -s /bin/sh -c "nova-manage db sync" nova`
  - Verify nova cell0 and cell1 are registered correctly: `su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova`

  ![](../images/OpenStack/Nova/1.png)

- Enable and start service
  - ` systemctl enable openstack-nova-api openstack-nova-scheduler openstack-nova-conductor openstack-nova-novncproxy libvirtd openstack-nova-compute`
  - ` systemctl start openstack-nova-api openstack-nova-scheduler openstack-nova-conductor openstack-nova-novncproxy libvirtd openstack-nova-compute` 

#### 1.4. Khai báo compute node vào database *cell*

- Đảm bảo rằng đã có compute hosts trong cơ sở dữ liệu: `openstack compute service list --service nova-compute`

![](../images/OpenStack/Nova/2.png)

- Mỗi khi thêm một compute node, phải chạy dòng lệnh:`su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova` trên controller node để khai báo cho compute node mới đó. Hoặc có thể cấu hình (trong file /etc/nova/nova.conf) controller tự đọng quét các compute khả dụng sau 1 khoảng thời gian nhất định(ví dụ 300s): 

```
[scheduler]
discover_hosts_in_cells_interval = 300
```

## 2.Verify operation<a name="2"></a>

- Kiểm tra các thành phần của nova: `openstack compute service list`

![](../images/OpenStack/Nova/3.png)

- Kiểm tra các API endpoints:`openstack catalog list`

![](../images/OpenStack/Nova/4.png)

