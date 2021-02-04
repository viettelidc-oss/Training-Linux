





# Hướng dẫn cài đặt CEPH Nautilus trên CentOS7

## 1. Mô hình dựng LAB

![](./Image/20.png)

![](./Image/21.png)

# 2. Cac buoc cai dat

## 2.1. Thiết lập hostname, IP cho node CEPH1

Login với tài khoản root và thực hiện các lệnh dưới.

```
yum update -y
```

Cài đặt các gói phần mềm bổ trợ

```
yum install epel-release -y
yum update -y
yum install wget byobu curl git byobu python-setuptools python-virtualenv -y
```

Cấu hình chế độ firewall để tiện trong môi trường lab. Trong môi trường production cần bật firewall hoặc iptables hoặc có biện pháp xử lý khác tương ứng để đảm bảo các vấn đề về an toàn.

```
sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo systemctl start network

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
```

Khai báo file /etc/hosts. Việc này rất quan trọng vì CEPH sẽ sử dụng hostname trong các bước tới để cấu hình và kết nối khi thực hiện.

```
cat << EOF > /etc/hosts
127.0.0.1 `hostname` localhost
192.168.2.112 client1
192.168.2.142 ceph1
192.168.2.132 ceph2
192.168.2.122 ceph3

192.168.237.112 client1
192.168.237.142 ceph1
192.168.237.132 ceph2
192.168.237.122 ceph3
EOF
```

Cài đặt NTP, trong hướng dẫn này sử dụng chronyd thay cho ntpd. Việc đồng bộ thời gian cũng là quan trọng khi triển khai CEPH. Hãy đảm bảo timezone và thời gian được đồng bộ để đúng với hệ thống

```
yum install -y chrony

systemctl enable chronyd.service
systemctl start chronyd.service
systemctl restart chronyd.service
chronyc sources
```

Cấu hình đồng bộ thời gian

 https://news.cloud365.vn/cai-dat-chrony-tren-centos-rhel-7/

Khởi động lại node CEPH1 và chuyển sang CEPH2 thực hiện tiếp.

```
init 6
```

Thiết lập hostname, IP cho node CEPH2 vaf CEPH 3 tuong tu

## 2.2. Tạo user cài đặt CEPH và khai báo repos

#### Tạo user để cài đặt CEPH trên cả 03 node CEPH1, CEPH2, CEPH3.

**Bước này được thực hiện trên cả 03 node CPEH.**

Tạo user là cephuser với mật khẩu là matkhau2019@

```
useradd cephuser; echo 'matkhau2019@' | passwd cephuser --stdin
```

Cấp quyền sudo cho user cephuser

```
echo "cephuser ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/cephuser
chmod 0440 /etc/sudoers.d/cephuser
```

![](./Image/22.png)

#### Khai báo repo cho ceph nautilus

Thực hiện hiện khai báo repo cho ceph nautilus trên tất cả các node CEPH.

```
cat <<EOF> /etc/yum.repos.d/ceph.repo
[ceph]
name=Ceph packages for $basearch
baseurl=https://download.ceph.com/rpm-nautilus/el7/x86_64/
enabled=1
priority=2
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[ceph-noarch]
name=Ceph noarch packages
baseurl=https://download.ceph.com/rpm-nautilus/el7/noarch
enabled=1
priority=2
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc

[ceph-source]
name=Ceph source packages
baseurl=https://download.ceph.com/rpm-nautilus/el7/SRPMS
enabled=0
priority=2
gpgcheck=1
gpgkey=https://download.ceph.com/keys/release.asc
EOF
```

![](./Image/23.png)

## 2.3. Cai đặt ceph-deploy và cấu hình

#### Cài đặt ceph-deploy.

**Thực hiện việc cài đặt này trên node CEPH1**

sử dụng node ceph1 chính là node ceph admin. Thực hiện việc này bằng tài khoản root.

Lưu ý: trong hướng dẫn này chỉ cần đứng trên ceph1 thực hiện, một số thao tác trên node CEPH2, CEPH3 sẽ thực hiện từ xa ngay trên CEPH1.

```
sudo yum install -y epel-release
sudo yum install -y ceph-deploy
```

Chuyển sang tài khoản `cephuser`

```
su - cephuser
```

```
ssh-keygen
```

Nhấn Enter để mặc định các tham số, bước này sẽ sinh ra private key và public key cho user *cephuser*. Sau đó tiến hành các lệnh dưới để copy public key sang các node.

Nhập mật khẩu của user *cephuser* tạo ở các node trước đó trong bước trên.

```
ssh-copy-id cephuser@ceph1
```

```
ssh-copy-id cephuser@ceph2
```

```
ssh-copy-id cephuser@ceph3
```

![](./Image/24.png)

Tạo thư mục chứa các file cấu hình khi cài đặt CEPH

```
cd ~
mkdir my-cluster
cd my-cluster
```

Khai báo các node ceph trong cluser.

```
ceph-deploy new ceph1 ceph2 ceph3
```

![](./Image/25.png)

Lệnh trên sẽ sinh ra các file cấu hình trong thư mục hiện tại, kiểm tra bằng lệnh *ls – alh*

![](./Image/26.png)

Khai báo thêm các tùy chọn cho việc triển khai, vận hành CEPH vào file ceph.conf này trước khi cài đặt các gói cần thiết cho ceph trên các node. Lưu ý các tham số về network.

```
echo "public network = 192.168.2.0/24" >> ceph.conf
echo "cluster network = 192.168.237.0/24" >> ceph.conf
echo "osd objectstore = bluestore"  >> ceph.conf
echo "mon_allow_pool_delete = true"  >> ceph.conf
echo "osd pool default size = 3"  >> ceph.conf
echo "osd pool default min size = 1"  >> ceph.conf
```

![](./Image/27.png)

Bắt đầu cài đặt phiên bản CEPH Nautilus lên các node ceph1, ceph2, ceph3. Lệnh dưới sẽ cài đặt lần lượt lên các node.

![](./Image/28.png)

![](./Image/29.png)

![](./Image/30.png)

Thiết lập thành phần MON cho CEPH. Trong hướng dẫn này khai báo 03 node đều có thành phần MON của CEPH.

```
ceph-deploy mon create-initial
```

![](./Image/31.png)

Kết quả sinh ra các file trong thư mục hiện tại

![](./image/32.png)

- Để node `ceph01`, `ceph02`, `ceph03` có thể thao tác với cluster chúng ta cần gán cho node quyền admin bằng cách bổ sung key `admin.keying` cho node

  

```
ceph-deploy admin ceph01 ceph02 ceph03
```

> ![](./image/33.png)

 

#### Cấu hình manager và dashboad cho ceph cluster

Ceph-dashboard là một thành phần thuộc `ceph-mgr`. Trong bản Nautilus thì thành phần dashboard được cả tiến khá lớn. Cung cấp nhiều quyền hạn thao tác với CEPH hơn các bản trước đó

Thực hiện trên node ceph1 việc cài đặt này.

Cài thêm các gói bổ trợ trước khi cài

```
sudo yum install -y python-jwt python-routes
sudo rpm -Uvh http://download.ceph.com/rpm-nautilus/el7/noarch/ceph-grafana-dashboards-14.2.3-0.el7.noarch.rpm

sudo rpm -Uvh http://download.ceph.com/rpm-nautilus/el7/noarch/ceph-mgr-dashboard-14.2.3-0.el7.noarch.rpm
```

Thực hiện kích hoạt ceph-mgr và ceph-dashboard

```
ceph-deploy mgr create ceph1 ceph2 ceph3
ceph mgr module enable dashboard --force
ceph mgr module ls
```

Tạo cert cho ceph-dashboad

```
sudo ceph dashboard create-self-signed-cert 
```

Kết quả trả về dòng *Self-signed certificate created* là thành công.

Tạo tài khoản cho ceph-dashboard, trong hướng dẫn này tạo tài khoản tên là *cephadmin* và mật khẩu là *matkhau2019@*

```
ceph dashboard ac-user-create cephadmin matkhau2019@ administrator 
```

```
ceph mgr services 
```

```
{
    "dashboard": "https://ceph1:8443/"
}
```

Truy cập https://ip_ceph1:8443

Màn hình trạng thái tổng thể của CEP cluster

![](./image/35.png)

Màn hình thành phần MON của CEPH

![](./image/36.png)

Màn hình các Node của CEPH

![](./image/37.png)

Màn hình các OSD disk của CEPH

![](./image/38.png)

#### Cấu hình object storage và khai báo sử dụng trên ceph-dashboard

 sử dụng node ceph2 để cài đặt thành phần Radosgw để cung cấp object storage.

Đứng trên ceph1 tiếp tục thực hiện lệnh dưới để triển khai thành phần radosgw. 

```
ceph-deploy install --rgw ceph2
```

```
ceph-deploy rgw create ceph2
```

![](./image/39.png)

Thực hiện khai báo user để có thể sử dụng được dashboard để quản lý object storage, ta sẽ có tùy chọn “*–system*“

```
radosgw-admin user create --uid=cloud365admin --display-name=Cloud365 --system
```

Kết quả trả về sẽ là thông tin về *access_key* và *secret_key* 

![](./image/40.png)

Thực hiện khai báo `access_key` và `secret_key`tích hợp với dashboard của ceph. Lưu ý phải dùng chuỗi đối với kết quả của bạn nhé.

```
ceph dashboard set-rgw-api-access-key CQF41G7RFFSXV37NXE82
ceph dashboard set-rgw-api-secret-key S7SC3kGkdT22Okw6cbwThYIxerPH40J3UZgLk44C
ceph dashboard set-rgw-api-ssl-verify False
```

Tới đây, ta chuyển sang bước login vào dashboard của CEPH để xem các thành phần của `object storage` vừa khai báo, cụ thể như sau

Màn hình quan sát các node đã cài đăt radosgw

![](./image/41.png)



Màn hình liệt kê user của object storage

![](./image/42.png)

Màn hình tạo user trong object storage

![](./image/43.png)

![](./image/44.png)





## Hướng dẫn sử dụng block storage của CEPH

**Cách dùng block storage được tổng hợp như sau**:

- Block storage được dùng để chứa máy ảo trong môi trường cloud hoặc là disk được gắn thêm cho máy ảo hoặc máy chủ thông thường, disk gắn thêm này được cấp ra từ cụm cluser CEPH.
- Muốn dùng block storage thì các client phải có gói phần mềm hỗ trợ, tức là phải cài phần mềm tương đương driver.
- Tốc độ truy cập của block storage nhanh hơn object storage nên phù hợp với các hệ thống đòi hỏi khả năng truy xuất cao như Database, hệ điều hành (chính là OS cài lên disk).
- Đa số các hệ thống cung cấp block storage chỉ cung cấp trong phạm vi khoảng cách ngắn, không như object là có thể qua môi trường internet.

### Cấu hình cơ bản cho `client1`

#### Khai báo IP, hostname

Như cấu hình giống ceph1

#### Cài đặt repos và các gói bổ trợ cho client1

Cài đặt các gói bổ trợ cho `client1`

```
yum update -y
yum install epel-release -y
yum install wget bybo curl git -y
yum install python-setuptools -y
yum install python-virtualenv -y
yum update -y
```

Cấu hình NTP

```
yum install -y chrony

systemctl enable chronyd.service
systemctl start chronyd.service
systemctl restart chronyd.service
chronyc sources
```

Tạo user là cephuser với mật khẩu là `matkhau2019@`

```
useradd cephuser; echo 'matkhau2019@' | passwd cephuser --stdin
echo "cephuser ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/cephuser
chmod 0440 /etc/sudoers.d/cephuser
```

```
cd /ceph-deploy
```

```
ssh-copy-id cephuser@client
```

```
ceph-deploy install --release nautilus client
```

![](./image/45.png)

Thực hiện deploy node `client`

```
ceph-deploy admin client
```

![](./image/46.png)

Đứng trên node `CEPH1` để phân quyền cho file `/etc/ceph/ceph.client.admin.keyring` cho node `client`

```
ssh cephuser@client 'sudo chmod +r /etc/ceph/ceph.client.admin.keyring'
```

#### Cấu hình RBD cho Client sử dụng

**Thực hiên trên node** `ceph1`

Khai báo pool tên là `rbd` để client sử dụng. Tên pool này khá đặc biệt vì nó là pool block storage mặc định của CEPH. Nếu trong các lệnh ko đưa tùy chọn `-p ten_pool` thì CEPH sẽ làm việc với pool này.

```
ceph osd pool create rbd 128
```

```
rbd pool init rbd
```

Kiểm tra pool vừa tạo xem đã có hay chưa bằng lệnh `ceph osd pool ls`

![](./image/47.png)

Tới đây đã thiết lập rbd pool xong trên node `CEPH1`. Chuyển sang client để sử dụng các images (tạm hiểu là các disk)

Đứng trên node cephclient1 thực hiện tạo một image có tên là disk01 với dung lượng là 10GB, image này sẽ nằm trong pool có tên là `rdbpool` vừa tạo ở trên.

```
 rbd create disk01 --size 10G --image-feature layering
```

Dùng lệnh liệt kê các images để kiểm tra lại xem các images RDB đã được tạo hay chưa

```
rbd ls -l
```

Kết quả lệnh `rbd ls -l -p rbd`

![](./image/48.png)

Việc tiếp theo cần phải gắn nó vào máy ảo và format, sau đó tiếp tục mount vào thư mục cần thiết.

Thực hiện map images đã được tạo tới một disk của máy client

```
rbd map disk01 
```

![](./image/49.png)

Thực hiện kiểm tra xem images RBD có tên là disk01 đã được map hay chưa.

```
rbd showmapped
```

![](./image/50.png)

Tới đây máy client chưa thể sử dụng ổ được map vì chưa được phân vùng, tiếp tục thực hiện bước phân vùng và mount vào một thư mục nào đó để sử dụng.

```
sudo mkfs.xfs /dev/rbd0
```

![](./image/51.png)

Thực hiện mount vào thư mục /mtn

```
sudo mount /dev/rbd0 /mnt
```

![](./image/52.png)

Kích hoạt rdb map

```
systemctl start rbdmap

systemctl enable rbdmap

systemctl status rbdmap
```

Sửa file `vi /etc/ceph/rbdmap` bằng việc thêm dòng `rbd/disk01 id=admin,keyring=/etc/ceph/ceph.client.admin.keyring` vào cuối file. Nội dung của file `/etc/ceph/rbdmap` như dưới

Sửa file `/etc/fstab` bằng việc khai báo đường dẫn của device và thư mục được mount. Thêm dòng dưới vào cuối cùng của file.

```
/dev/rbd/rbd/disk01             /mnt                    xfs     noauto          0 0
```

#### Thực hiện tạo ra pool thứ 2 cho block storage

```
ceph osd pool create rbdpool 64

rbd pool init rbdpool
```

Kiểm tra lại các pool đã được tạo hay chưa bằn lệnh `ceph osd pool ls`. Ta có kết quả dưới.

![](./image/53.png)

Chuyển sang node `client` thực hiện các bước dưới.

Thực hiện khai báo cho client sử dụng pool có tên là `rbdpool`. Đứng trên `client` thực hiện với tùy chọn `-p rbdpoll`

```
rbd create disk02 --size 2G --image-feature layering -p rbdpool
```

Kiểm tra image có tên là `disk02` xem được tạo hay chưa bằng lệnh `rbd ls -l -p rbdpool` . Kết quả là

![](./image/54.png)

Thực hiện map image disk02 để client1 sử dụng. Lưu ý chỉ định tùy chọn `-p rbdpool`

```
rbd map disk02 -p rbdpool
```

![](./image/55.png)

Thực hiện format `/dev/rdb1` để bắt đầu sử dụng.

```
sudo mkfs.xfs /dev/rbd1
```

Thực hiện mount thư mục `/opt` của client1 để dùng.

```
sudo mount /dev/rbd1 /opt
```

Thử ghi dữ liệu và quan sát ở thư mục `/opt`

```
echo "Cloud365 chao cac ban" > /opt/ceph.txt
cat /opt/ceph.txt
Cloud365 chao cac ban
```