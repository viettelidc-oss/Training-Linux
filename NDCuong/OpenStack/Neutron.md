# Placement

#### [1.Install and configure](#1)



------------------------------------------------------

## 1.Install and configure<a name="1"></a>

#### 1.1. Tạo database

- Kết nối với máy chủ cơ sở dữ liệu với tư cách root: `mysql -u root -p` => Nhập Password đã đặt khi cài đặt [môi trường](./Môi%20trường.md#5) 

- Tạo database: `CREATE DATABASE neutron;`

- Cấp quyền truy cập vào database

  - `GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \
    IDENTIFIED BY 'NEUTRON_DBPASS';` 
    
  - `GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \
        IDENTIFIED BY 'NEUTRON_DBPASS';`
  
  - Lưu ý: thay NEUTRON_DBPASSbằng mật khẩu muốn đặt
  

#### 1.2. Xác thực, ủy quyền với KeyStone

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

  

#### 1.3. Cài đặt và cấu hình Neutron

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
