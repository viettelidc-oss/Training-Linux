# Horizon

#### [1.Install and configure](#1)



------------------------------------------------------

## 1.Install and configure<a name="1"></a>

#### 1.1. Cài đặt và cấu hình Horizon

- Install packages : `yum install openstack-dashboard`

- Config: `vi  /etc/openstack-dashboard/local_settings`

  - Input: 

    ```
     OPENSTACK_HOST = "controller"
     ALLOWED_HOSTS = ['one.example.com', 'two.example.com']
     SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
    WEBROOT = "/dashboard/"
    CACHES = {
        'default': {
             'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
             'LOCATION': 'controller:11211',
        }
    }
    OPENSTACK_KEYSTONE_URL = "http://%s:5000/v3" % OPENSTACK_HOST
    OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True
    OPENSTACK_API_VERSIONS = {
        "identity": 3,
        "image": 2,
        "volume": 2,
    }
    OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"
    OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"
    TIME_ZONE = "Asia/Ho_Chi_Minh"
    ```

- Config: `vi /etc/httpd/conf.d/openstack-dashboard.conf`

  - Input : `WSGIApplicationGroup %{GLOBAL}`

- Restart service httpd, memcached
