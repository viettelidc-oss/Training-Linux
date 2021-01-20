Glance 

```
mysql -u root -pWelcome123
CREATE DATABASE glance;
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'Welcome123';
exit
```

```
openstack user create glance --domain default --password Welcome123
```

![image-20210104162058867](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210104162058867.png)

```
openstack role add --project service --user glance admin
```

![image-20210104162144900](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210104162144900.png)

![image-20210104162236867](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210104162236867.png)

![image-20210104162354315](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210104162354315.png)

```
yum install openstack-glance -y
```

![image-20210104170015457](C:\Users\ADMIN\AppData\Roaming\Typora\typora-user-images\image-20210104170015457.png)