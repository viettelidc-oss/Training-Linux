#### Glance

systemctl restart openstack-glance-api

systemctl restart openstack-glance-registry

#### Nova

systemctl restart openstack-nova-api

systemctl restart openstack-nova-conductor

systemctl restart openstack-nova-scheduler

systemctl restart openstack-nova-novncproxy

systemctl restart openstack-nova-compute

systemctl restart libvirtd

#### Neutron

systemctl restart neutron-server

systemctl restart neutron-linuxbridge-agent

systemctl restart neutron-dhcp-agent

systemctl restart neutron-metadata-agent

systemctl restart neutron-l3-agent

#### Cinder

systemctl restart openstack-cinder-api

systemctl restart openstack-cinder-scheduler

systemctl restart openstack-cinder-volume

systemctl restart target

#### Else

systemctl restart httpd

systemctl restart memcached