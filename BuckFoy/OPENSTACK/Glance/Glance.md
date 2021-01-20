# Glance 
### tạo database
Tạo database: CREATE DATABASE glance;

Cấp quyền truy cập vào database


```
mysql -u root -pWelcome123
CREATE DATABASE glance;
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'Welcome123';
exit
```

### Xác thực, ủy quyền với KeyStone

Chạy file client environment scripts admin-openrc để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): . admin-openrc

Tạo user glance: openstack user create --domain default --password-prompt glance
```

openstack user create glance --domain default --password Welcome123
```

![](./Glance/1.png)

Tạo role phân quyền project service (tạo mới nếu chưa có: openstack project create --domain default --description "Service Project" service) cho user glance: openstack role add --project service --user glance admin

Tạo service (type image) glance : openstack service create --name glance --description "OpenStack Image" image

Tạo API endpoints(vị trí mà các API tương tác với hệ thống, truy cập tài nguyên. Tham khảo):

endpoint public: openstack endpoint create --region RegionOne image public http://controller:9292
endpoint internal: openstack endpoint create --region RegionOne image internal http://controller:9292
endpoint admin: openstack endpoint create --region RegionOne image admin http://controller:9292

![](./Glance/2.png)

![](./Glance/3.png)

![](./Glance/4.png)


## Cài đặt và cấu hình Glance
```
yum install openstack-glance -y
```
![](./Glance/5.png)

Config: vi /etc/glance/glance-api.conf

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

Tạo bảng cho dịch vụ Image: su -s /bin/sh -c "glance-manage db_sync" glance - Các bảng sau sẽ tự động được tạo khi chạy lệnh trên

Enable and start service
systemctl enable openstack-glance-api.service
systemctl start openstack-glance-api.service
