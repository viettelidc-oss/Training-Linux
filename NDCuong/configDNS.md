## Cấu hình

#### /etc/bind/named.conf.ontions

> ![](./images/dns/confop.png)

#### /etc/bind/named.conf

> ![](./images/dns/conf.png)

#### /etc/bind/named.conf.local

> ![](./images/dns/conflocal.png)

- "ze9hyrus.com" : domain zone
- "etc/bind/forward.ze9hyrus": tên file và địa chỉ tuyệt đối của file forward (tương tự với "etc/bind/reverse.ze9hyrus")

#### /etc/bind/forward.ze9hyrus (tạo file mới)

> ![](./images/dns/fw.png)
- masterdns.ze9hyrus.com: hostname server master
- slavedns.ze9hyrus.com: hostname server slave
- ...131: địa chỉ ip server master
- ...130: địa chỉ ip server slave
- ...129: địa chỉ ip client
#### /etc/bind/reverse.ze9hyrus (tạo file mới)

> ![](./images/dns/rv.png)

#### /etc/network/interfaces

> ![](./images/dns/itf.png)

#### /etc/resolv.conf 

> ![](./images/dns/rs.png)

## Kết quả

#### Check file conf, zone

> ![](./images/dns/check.png)

#### Check server

> ![](./images/dns/result.png)
