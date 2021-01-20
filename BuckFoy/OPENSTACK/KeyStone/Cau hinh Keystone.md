##

**Cài đặt và cấu hình SQL database**

Hầu hết các dịch vụ OpenStack sử dụng cơ sở dữ liệu SQL để lưu trữ thông tin. Cơ sở dữ liệu thường chạy trên controller node.

```
yum install mariadb mariadb-server python2-PyMySQL -y
```

Tạo file `/etc/my.cnf.d/openstack.cnf

```
vi /etc/my.cnf.d/openstack.cnf
```

thay [IP controller node] bằng ip của controller node

```
[mysqld]

bind-address = 192.168.237.142
default-storage-engine = innodb
innodb_file_per_table
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```

```
systemctl enable mariadb.service
systemctl start mariadb.service
```

Config secure: `mysql_secure_installation`

- Nhập password (ở lần đầu đăng nhập sau khi cài mariadb, password sẽ trống, chỉ cần nhấn enter)
- Thay đổi mật khẩu (option: y-có, n-không)
- Người dùng ẩn danh: cho phép bất kỳ ai để đăng nhập vào MariaDB mà không cần phải tạo tài khoản người dùng. Điều này chỉ nhằm mục đích thử nghiệm và thực hiện cài đặt đi mượt mà hơn một chút. Bạn nên xóa chúng trước khi sử dụng. (option: y-xóa, n-không)
- Root: Thông thường, root chỉ được phép kết nối từ 'localhost'. Điều này đảm bảo rằng ai đó không thể đoán được mật khẩu gốc từ mạng. (option: y-chặn root truy cập từ xa, n-cho phép root truy cập từ xa)
- Database "test": Theo mặc định, MariaDB đi kèm với một cơ sở dữ liệu có tên 'test' mà bất kỳ ai cũng có thể truy cập. Điều này cũng chỉ nhằm mục đích thử nghiệm và nên được loại bỏ. (option: y-xóa, n-không)
- Reload: tải lại và áp dụng các thay đổi (option: y-có, n-không)

![](./Image/1.png)

![](./Image/2.png)
### Message queue

OpenStack sử dụng hàng đợi tin nhắn để điều phối hoạt động và thông tin trạng thái giữa các dịch vụ. Hướng dẫn này triển khai dịch vụ hàng đợi tin nhắn RabbitMQ vì hầu hết các bản phân phối đều hỗ trợ nó.

- Install : `yum install rabbitmq-server`, khởi động dịch vụ `systemctl start rabbitmq-server.service`
- Thêm user 'openstack': `rabbitmqctl add_user openstack RABBIT_PASS` - thay RABBIT_PASS bằng mật khẩu muốn đặt
- Cấp quyền: `rabbitmqctl set_permissions openstack ".*" ".*" ".*"`


```
yum install rabbitmq-server
```

```
systemctl enable rabbitmq-server.service
systemctl start rabbitmq-server.service
```
![](./Image/3.png)

#### Memcached

Cơ chế xác thực dịch vụ Identity cho các dịch vụ sử dụng Memcached để lưu trữ mã thông báo. Dịch vụ memcached thường chạy trên controller node.

- Install: `yum install memcached python-memcached`
- Config: `vi /etc/sysconfig/memcached` => input `OPTIONS="-l 127.0.0.1,::1,controller"` (replace `OPTIONS="-l 127.0.0.1,::1"` nếu có )
- Restart service

```

cp /etc/sysconfig/memcached /etc/sysconfig/memcached.origin
```
![](./Image/4.png)


```
systemctl enable memcached.service
systemctl start memcached.service
```

#### Etcd

Các dịch vụ OpenStack có thể sử dụng Etcd, một kho lưu trữ dạng khóa-giá trị (key-value) đáng tin cậy được phân phối để khóa khóa phân tán, lưu trữ cấu hình, theo dõi dịch vụ trực tiếp và các tình huống khác.

- Install: `yum install etcd`
- Config: `vi /etc/etcd/etcd.conf` => Gán giá trị cho các tham số sau:

```
#[Member]
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://[IP controller node]:2380"
ETCD_LISTEN_CLIENT_URLS="http://[IP controller node]:2379"
ETCD_NAME="controller"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://[IP controller node]:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://[IP controller node]:2379"
ETCD_INITIAL_CLUSTER="controller=http://[IP controller node]:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
ETCD_INITIAL_CLUSTER_STATE="new"
```

Thay [IP controller node] bằng địa chỉ ip của controller node

##  Cấu  hình Keystone

#### Tạo database 

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](https://github.com/ze9hyrus/Training-Linux/blob/main/NDCuong/OpenStack/Môi trường.md#5)
- Tạo database: `CREATE DATABASE keystone;`
- Cấp quyền truy cập vào database
  - `GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'KEYSTONE_DBPASS';`
  - `GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'KEYSTONE_DBPASS';`
  - Lưu ý: thay KEYSTONE_DBPASS bằng mật khẩu muốn đặt

![](./Image/5.png)

Install packages

```
yum install -y openstack-keystone httpd mod_wsgi
```

```
cp /etc/keystone/keystone.conf /etc/keystone/keystone.conf.origin
```

```
[database]
...
connection = mysql+pymysql://keystone:Welcome123@192.168.237.142/keystone

[token]
...
provider = fernet
```

```
Tạo bảng cho dịch vụ Identity:
su -s /bin/sh -c "keystone-manage db_sync" keystone
```



![](./Image/6.png)

Initialize Fernet key repositories (Khởi tạo kho khóa Fernet):

- `keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone`
- `keystone-manage credential_setup --keystone-user keystone --keystone-group keystone`
- Các tham số --keystone-user và keystone-group chỉ định user/group của hệ điều hành được phép chạy KeyStone. Ở đây user/group được chọn là 'keystone

Bootstrap the Identity service(khởi tạo, khai báo các tham số):

```
keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne
```

####  Cấu hình Apache HTTP server

 Sao lưu file `/etc/httpd/conf/httpd.conf`

```
cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.origin
```

Chỉnh sửa file `/etc/httpd/conf/httpd.conf`

```
vi /etc/httpd/conf/httpd.conf

...
ServerName controller
```

Tạo liên kết tới file `/usr/share/keystone/wsgi-keystone.conf`

```
ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/
```

```
systemctl enable httpd.service
systemctl start httpd.service
```

```
Cấu hình tài khoản admin:
export OS_USERNAME=admin
export OS_PASSWORD=Welcome123
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_AUTH_URL=http://192.168.237.142:35357/v3
export OS_IDENTITY_API_VERSION=3
```

```
openstack project create --domain default --description "Service Project" service
```

### tạo domain, project, user và roles

![](./Image/7.png)

![](./Image/8.png)

![](./Image/9.png)

![](./Image/10.png)

![](./Image/11.png)
