NOVA

#### Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt 
- Tạo database:
  - `CREATE DATABASE nova_api;`
  - `CREATE DATABASE nova;`
  - `CREATE DATABASE nova_cell0;`
- Cấp quyền truy cập vào database
=======

## Tao database


```
mysql -u root -pWelcome123
CREATE DATABASE nova_api;
CREATE DATABASE nova;
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' IDENTIFIED BY 'Welcome123';
exit
```
###  Xác thực, ủy quyền với KeyStone
Chạy file client environment scripts admin-openrc để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): . admin-openrc
Tạo user nova: openstack user create --domain default --password-prompt nova

```
. admin-openrc
openstack user create --domain default --password-prompt nova
```

![](./Image/1.png)

Tạo role phân quyền project service cho user nova: openstack role add --project service --user nova admin

Tạo service (type compute) nova : openstack service create --name nova --description "OpenStack Compute" compute

![](./Image/2.png)

Tạo API endpoints(vị trí mà các API tương tác với hệ thống, truy cập tài nguyên. Tham khảo):

```
openstack endpoint create --region RegionOne compute public http://192.168.237.142:8774/v2.1/%\(tenant_id\)s
openstack endpoint create --region RegionOne compute internal http://192.168.237.142:8774/v2.1/%\(tenant_id\)s
openstack endpoint create --region RegionOne compute admin http://192.168.237.142:8774/v2.1/%\(tenant_id\)s
```

![](./Image/3.png)


## Cài đặt và cấu hình Nova
```
yum install openstack-nova-api openstack-nova-conductor openstack-nova-console openstack-nova-novncproxy openstack-nova-scheduler -y
```

```
[DEFAULT]
auth_strategy = keystone
enabled_apis = osapi_compute,metadata
transport_url = rabbit://openstack:Welcome123@controller
my_ip = 192.168.100.197
use_neutron = True
firewall_driver = nova.virt.firewall.NoopFirewallDriver

[api_database]
connection = mysql+pymysql://nova:Welcome123@192.168.237.142/nova_api

[database]
connection = mysql+pymysql://nova:Welcome123@192.168.237.142/nova

[keystone_authtoken]
auth_uri = http://192.168.237.142:5000
auth_url = http://192.168.237.142:5000
memcached_servers = 192.168.237.142:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = Welcome123

[vnc]
vncserver_listen = $my_ip
vncserver_proxyclient_address = $my_ip

[glance]
api_servers = http://192.168.237.142:9292

[oslo_concurrency]
lock_path = /var/lib/nova/tmp

[libvirt]
# ...
virt_type = qemu
```

