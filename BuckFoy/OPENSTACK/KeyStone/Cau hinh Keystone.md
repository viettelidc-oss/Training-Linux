**Cài đặt và cấu hình SQL database**

```
yum install mariadb mariadb-server python2-PyMySQL -y
```

Tạo file `/etc/my.cnf.d/openstack.cnf`

```
vi /etc/my.cnf.d/openstack.cnf
```

```
[mysqld]

bind-address = 192.168.100.197
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

![](./Image/1.png)

![](./Image/2.png)


```
yum install rabbitmq-server
```

```
systemctl enable rabbitmq-server.service
systemctl start rabbitmq-server.service
```
![](./Image/3.png)

```
cp /etc/sysconfig/memcached /etc/sysconfig/memcached.origin
```
![](./Image/4.png)


```
systemctl enable memcached.service
systemctl start memcached.service
```

![](./Image/5.png)

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
su -s /bin/sh -c "keystone-manage db_sync" keystone
```

![](./Image/6.png)
```
keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne
```

#### 1.3. Cấu hình Apache HTTP server

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

![](./Image/7.png)

![](./Image/8.png)

![](./Image/9.png)

![](./Image/10.png)

![](./Image/11.png)
