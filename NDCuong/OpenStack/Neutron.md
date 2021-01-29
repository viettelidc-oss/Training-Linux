# Neutron

------------------------------------------------------

## 1.Neutron

#### 1.1. Giới thiệu chung

OpenStack Networking cho phép bạn tạo và quản lí các network objects ví dụ như networks, subnets, và ports; cho phép tạo và gắn các interface, card mạng vào VM

Với Neutron, bạn có thể tạo và cấu hình các networks, subnets và thông báo tới Compute để gán các thiết bị ảo vào các ports của mạng vừa tạo. OpenStack Compute và Neutron liên kết với nhau để cung cấp kết nối mạng cho các máy ảo. Cụ thể hơn, OpenStack Networking hỗ trợ cho phép các projects có nhiều private networks và các projects có thể tự chọn danh sách IP cho riêng mình, kể cả những IP đã được sử dụng bởi một project khác.

#### 1.2. Thành phần

- **API server**

  - Layer2 networking, còn được gọi là lớp liên kết dữ liệu(data link layer), là lớp mạng dùng để truyền dữ liệu giữa các nút mạng trên mạng diện rộng hoặc giữa các nút trong cùng một mạng cục bộ.
  - IP address management (IPAM - quản lý địa chỉ IP) - quản lý DNS và DHCP - các dịch vụ mạng gán và phân giải địa chỉ IP cho các máy trong mạng TCP / IP.
  - Một extension để xây dựng router layer 3 (network layer) cho phép định tuyến giữa các networks Layer 2 và các gateway để ra mạng bên ngoài.
  - Danh sách các plug-in cho phép tương tác với nhiều công nghệ mạng mã nguồn mở và cả thương mại, bao gồm các routers, switches, switch ảo và SDN controller.

- **OpenStack Networking plug-in and agents**

  Các plugin và các agent này cho phép gắn và gỡ các ports, tạo ra network hay subnet, và đánh địa chỉ IP. Lựa chọn plugin và agents nào là tùy thuộc vào nhà cung cấp và công nghệ sử dụng trong hệ thống cloud nhất định. Điều quan trọng là tại một thời điểm chỉ sử dụng được một plug-in.

- **Messaging queue**

  Tiếp nhận và định tuyến các RPC requests giữa các agents để hoàn thành quá trình vận hành API. Các Message queue được sử dụng trong ML2 plugin để thực hiện truyền thông RPC giữa neutron server và các neutron agents chạy trên mỗi hypervisor, cụ thể là các ML2 driver cho Open vSwitch và Linux bridge.

#### 1.3. Các khái niệm liên quan

Có hai loại network đó là self-service và provider.

- **Provider networks**

  Provider networks cung cấp kết nối layer 2 cho các máy ảo với các tùy chọn hỗ trợ cho dịch vụ DHCP và metadata. Các kết nối này thường sử dụng VLAN (802.1q) để nhận diện và tách biệt nhau.

  Nhìn chung, Provider networks cũng cấp sự đơn giản, hiệu quả và sự minh bạch, linh hoạt trong chi phí. Mặc định chỉ có duy nhất người quản trị mới có thể tạo hoặc cập nhật provider networks bởi nó yêu cầu phải cấu hình thiết bị vật lí. Bên cạnh đó, provider networks chỉ quản lí kết nối ở layer 2 cho máy ảo, vì thế nó thiếu đi một số tính năng ví dụ như định tuyến và gán floating IP.

- **Self-service networks**

  Self-service networks được ưu tiên ở các projects thông thường để quản lí networks mà không cần quản trị viên (quản lí network trong project). Các networks này là ảo và nó yêu cầu các routers ảo để giao tiếp với provider và external networks. Self-service networks cũng đồng thời cung cấp dịch vụ DHCP và metadata cho máy ảo.

  Trong hầu hết các trường hợp, self-service networks sử dụng các giao thức như VXLAN hoặc GRE bởi chúng hỗ trợ nhiều hơn là VLAN tagging (802.1q). Bên cạnh đó, Vlans cũng thường yêu cầu phải cấu hình thêm ở tầng vật lí.

  Với IPv4, self-service networks thường sử dụng dải mạng riêng và tương tác với provider networks thông qua cơ chế NAT trên router ảo. Floating IP sẽ cho phép kết nối tới máy ảo thông qua địa chỉ NAT trên router ảo. Trong khi đó, IPv6 self-service networks thì lại sử dụng dải IP public và tương tác với provider networks bằng giao thức định tuyến tĩnh qua router ảo.

  Các user có thể tạo các self-service networks cho các kết nối bên trong project. Mặc định thì các kết nối này là riêng biệt và không được chia sẻ giữa các project. OpenStack Networking hỗ trợ các công nghệ dưới đây cho self-service network:

  - Flat: Tất cả các instances nằm trong cùng một mạng, và có thể chia sẻ với hosts. Không hề sử dụng VLAN tagging hay hình thức tách biệt về network khác.
  - VLAN: Kiểu này cho phép các users tạo nhiều provider hoặc self-service network sử dụng VLAN IDs(chuẩn 802.1Q tagged) tương ứng với VLANs trong mạng vật lý. Điều này cho phép các instances giao tiếp với nhau trong môi trường cloud. Chúng có thể giao tiếp với servers, firewalls, load balancers vật lý và các hạ tầng network khác trên cùng một VLAN layer 2.
  - GRE và VXLAN: VXLAN và GRE là các **giao thức đóng gói tạo nên overlay networks** để kích hoạt và kiểm soát việc truyền thông giữa các máy ảo (instances). Một router được yêu cầu để cho phép lưu lượng đi ra luồng bên ngoài tenant network GRE hoặc VXLAN. Router cũng có thể yêu cầu để kết nối một tenant network với mạng bên ngoài (ví dụ Internet). Router cung cấp khả năng kết nối tới instances trực tiếp từ mạng bên ngoài sử dụng các địa chỉ floating IP.
  - Subnets: Là một **khối tập hợp các địa chỉ IP và đã được cấu hình**. Quản lý các địa chỉ IP của subnet do IPAM driver thực hiện. Subnet được dùng để cấp phát các địa chỉ IP khi ports mới được tạo trên network.
  - Subnet pools: Người dùng cuối thông thường có thể tạo các subnet với bất kì địa chỉ IP hợp lệ nào mà không bị hạn chế. Tuy nhiên, trong một vài trường hợp, sẽ là ổn hơn nếu như admin hoặc tenant định nghĩa trước một pool các địa chỉ để từ đó tạo ra các subnets được cấp phát tự động. Sử dụng subnet pools sẽ **ràng buộc những địa chỉ nào có thể được sử dụng bằng cách định nghĩa rằng mỗi subnet phải nằm trong một pool được định nghĩa trước**. Điều đó ngăn chặn việc tái sử dụng địa chỉ hoặc bị chồng lấn hai subnets trong cùng một pool.
  - Ports: Là **điểm kết nối** để attach một thiết bị như card mạng của máy ảo tới mạng ảo. Port cũng được cấu hình các thông tin như địa chỉ MAC, địa chỉ IP để sử dụng port đó.
  - Router: **cung cấp các dịch vụ layer 3** ví dụ như định tuyến, NAT giữa các self service và provider network hoặc giữa các self service với nhau trong cùng một project.
  - Security groups: Một security groups được coi như một **firewall ảo cho các máy ảo** để kiểm soát lưu lượng bên trong và bên ngoài router. Security groups hoạt động mức port, không phải mức subnet, do đó mỗi port trên một subnet có thể được gán với một tập hợp các security groups riêng. Nếu không chỉ định group cụ thể nào khi vận hành, máy ảo sẽ được gán tự động với default security group của project. Mặc định, group này sẽ hủy tất cả các lưu lượng vào và cho phép lưu lượng ra ngoài. Các rule có thể được bổ sung để thay đổi các hành vi đó. Security group và các security group rule cho phép người quản trị và các tenant chỉ định loại traffic và hướng (ingress/egress) được phép đi qua port. Một security group là một container của các security group rules. Các rules trong security group phụ thuộc vào nhau. Vì thế nếu bạn cho phép inbound TCP port 22, hệ thống sẽ tự động tạo ra 1 rule cho phép outbound traffic trả lại và ICMP error messages liên quan tới các kết nối TCP vừa được tạo rules.
  - Extensions: OpenStack Networking service có khả năng mở rộng. Có hai mục đích chính cho việc này: cho phép thực thi các tính năng mới trên API mà không cần phải đợi đến khi ra bản tiếp theo và cho phép các nhà phân phối bổ sung những chức năng phù hợp. Các ứng dụng có lấy danh sách các extensions có sẵn sử dụng phương thức GET trên /extensions URI. Chú ý đây là một request phụ thuộc vào phiên bản OpenStack, một extension trong một API ở phiên bản này có thể không sử dụng được cho phiên bản khác.
  - DHCP: Dịch vụ tùy chọn DHCP **quản lí địa chỉ IP** trên provider và self-service networks. Networking service triển khai DHCP service sử dụng agent quản lí qdhcp namespaces và dnsmasq service.
  - Metadata: Dịch vụ tùy chọn cung cấp API cho máy ảo để lấy metadata ví dụ như SSH keys.
  - L3 Agent: L3 agent là một phần của package openstack-neutron. Nó được xem như router layer3 chuyển hướng lưu lượng và cung cấp dịch vụ gateway cho network lớp 2. Các nodes chạy L3 agent không được cấu hình IP trực tiếp trên một card mạng mà được kết nối với mạng ngoài. Thay vì thế, sẽ có một dải địa chỉ IP từ mạng ngoài được sử dụng cho OpenStack networking. Các địa chỉ này được gán cho các routers mà cung cấp liên kết giữa mạng trong và mạng ngoài. Miền địa chỉ được lựa chọn phải đủ lớn để cung cấp địa chỉ IP duy nhất cho mỗi router khi triển khai cũng như mỗi floating IP gán cho các máy ảo.

## 2.Install and configure<a name="1"></a>

#### 2.1. Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database: `CREATE DATABASE neutron;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \
    IDENTIFIED BY 'NEUTRON_DBPASS';` 
    
  - `GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \
        IDENTIFIED BY 'NEUTRON_DBPASS';`
  
  - Lưu ý: thay NEUTRON_DBPASSbằng mật khẩu muốn đặt
  

#### 2.2. Xác thực, ủy quyền với KeyStone

- Chạy file client environment scripts **admin-openrc** để xác thực với người dùng admin để có thể dùng các lệnh CLI chỉ dành cho quản trị viên (admin-only CLI commands): `. admin-openrc`
- Tạo user *placement*: `openstack user create --domain default --password-prompt neutron`

![](../images/OpenStack/Neutron/nu.png)

- Tạo role phân quyền project *service* cho user *placement*: `openstack role add --project service --user neutron admin`
- Tạo service (type placement) *placement* :  `openstack service create --name network --description "Neutron API" neutron`

![](../images/OpenStack/Neutron/ns.png)

[Tham khảo các option tạo service](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/service.html)

- Tạo Neutron API endpoints:

  - endpoint public: `openstack endpoint create --region RegionOne network public http://controller:9696`
  - endpoint internal: `openstack endpoint create --region RegionOne network internal http://controller:9696`
  - endpoint admin: `openstack endpoint create --region RegionOne network admin http://controller:9696`

  ![](../images/OpenStack/Neutron/ep.png)

  

#### 2.3. Cài đặt và cấu hình Neutron

- Install packages : `yum install openstack-neutron openstack-neutron-ml2 openstack-neutron-linuxbridge ebtables`

- Config: `vi  /etc/neutron/neutron.conf`

  - Input: 

    ```
    [DEFAULT]
    # ...
    core_plugin = ml2
    service_plugins = router
    allow_overlapping_ips = true
    auth_strategy = keystone
    transport_url = rabbit://openstack:RABBIT_PASS@controller
    notify_nova_on_port_status_changes = true
    notify_nova_on_port_data_changes = true
    
    
    [database]
    # ...
    connection = mysql+pymysql://neutron:NEUTRON_DBPASS@controller/neutron
    
    
    [keystone_authtoken]
    # ...
    www_authenticate_uri = http://controller:5000
    auth_url = http://controller:5000
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    project_name = service
    username = neutron
    password = NEUTRON_PASS
    
    [nova]
    # ...
    auth_url = http://controller:5000
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    region_name = RegionOne
    project_name = service
    username = nova
    password = NOVA_PASS
    
    [neutron]
    # ...
    url = http://controller:9696
    auth_url = http://controller:5000
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    region_name = RegionOne
    project_name = service
    username = neutron
    password = NEUTRON_PASS
    service_metadata_proxy = true
    metadata_proxy_shared_secret = METADATA_SECRET
    
    [oslo_concurrency]
    # ...
    lock_path = /var/lib/neutron/tmp
    ```
    
    Thay các biến *_PASS bằng mật khẩu tương ứng 

- Config ML2 plug-in: `vi /etc/neutron/plugins/ml2/ml2_conf.ini`

  - Input:

```
[ml2]
# ...
type_drivers = flat,vlan,vxlan
tenant_network_types = vxlan
mechanism_drivers = linuxbridge,l2population
extension_drivers = port_security

[ml2_type_flat]
# ...
flat_networks = provider

[ml2_type_vxlan]
# ...
vni_ranges = 1:1000

[securitygroup]
# ...
enable_ipset = true
```



- Configure the Linux bridge agent: `vi /etc/neutron/plugins/ml2/linuxbridge_agent.ini`

  - Input

  ```
  [linux_bridge]
  physical_interface_mappings = provider:PROVIDER_INTERFACE_NAME
  
  [vxlan]
  enable_vxlan = true
  local_ip = OVERLAY_INTERFACE_IP_ADDRESS
  l2_population = true
  
  [securitygroup]
  # ...
  enable_security_group = true
  firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver
  ```

  - PROVIDER_INTERFACE_NAME: Interface dùng làm bridge(VD: ens33)

  - OVERLAY_INTERFACE_IP_ADDRESS: IP API endpoint

  - Ensure your Linux operating system kernel supports network bridge filters by verifying all the following sysctl values are set to 1:

    - `vi /etc/sysctl.conf`
    - Input:

    ```
    net.bridge.bridge-nf-call-iptables = 1
    net.bridge.bridge-nf-call-ip6tables = 1
    ```

- Configure the layer-3 agent: `vi /etc/neutron/l3_agent.ini`

  - Input:

  ```
  [DEFAULT]
  # ...
  interface_driver = linuxbridge
  ```

- Configure the DHCP agent: `vi /etc/neutron/dhcp_agent.ini`

  - Input: 

    ```
    [DEFAULT]
    # ...
    interface_driver = linuxbridge
    dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
    enable_isolated_metadata = true
    ```

- Configure the metadata agent: `vi /etc/neutron/metadata_agent.ini`

  - Input:

  ```
  [DEFAULT]
  # ...
  nova_metadata_host = controller
  metadata_proxy_shared_secret = METADATA_SECRET
  ```

- Link file config plugin: `ln -s /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugin.ini`

- Tạo bảng cho dịch vụ Image: `su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron` 

- Restart service: 
  -  `systemctl restart openstack-nova-api`
  - `systemctl enable neutron-server.service \
      neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
      neutron-metadata-agent.service`
  - `systemctl start neutron-server.service \
      neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
      neutron-metadata-agent.service`
  - `systemctl enable neutron-l3-agent.service`
  - `systemctl start neutron-l3-agent.service`
