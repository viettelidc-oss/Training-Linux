NOVA

#### Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](https://github.com/ze9hyrus/Training-Linux/blob/main/NDCuong/OpenStack/Môi trường.md#5)
- Tạo database:
  - `CREATE DATABASE nova_api;`
  - `CREATE DATABASE nova;`
  - `CREATE DATABASE nova_cell0;`
- Cấp quyền truy cập vào database

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

```
. admin-openrc
openstack user create --domain default --password-prompt nova
```

![image-20210106110857229](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210106110857229.png)

![image-20210106110932045](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210106110932045.png)

```
openstack endpoint create --region RegionOne compute public http://192.168.237.142:8774/v2.1/%\(tenant_id\)s
openstack endpoint create --region RegionOne compute internal http://192.168.237.142:8774/v2.1/%\(tenant_id\)s
openstack endpoint create --region RegionOne compute admin http://192.168.237.142:8774/v2.1/%\(tenant_id\)s
```

![image-20210106111136036](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210106111136036.png)

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
```