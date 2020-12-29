## Cấu hình master server

#### `vi /etc/bind/named.conf.ontions`

> ![](../images/dns/confop.png)

#### `vi /etc/bind/named.conf`<a name="1"></a>

> ![](../images/dns/conf.png)

#### `vi /etc/bind/named.conf.local`

> ![](../images/dns/conflocal.png)

- "ze9hyrus.com" : domain zone
- "etc/bind/forward.ze9hyrus": tên file và địa chỉ tuyệt đối của file forward (tương tự với "etc/bind/reverse.ze9hyrus")

#### `vi /etc/bind/forward.ze9hyrus` (tạo file mới)

> ![](../images/dns/fw.png)
- masterdns.ze9hyrus.com: hostname server master
- slavedns.ze9hyrus.com: hostname server slave
- ...131: địa chỉ ip server master
- ...130: địa chỉ ip server slave
- ...129: địa chỉ ip client
#### `vi /etc/bind/reverse.ze9hyrus `(tạo file mới)

> ![](../images/dns/rv.png)

#### `vi /etc/network/interfaces`
Đảm bảo rằng hệ thống đã cài đặt package *ifupdown* trước khi gõ lệnh này

> ![](../images/dns/itf.png)

#### `vi /etc/resolv.conf `

> ![](../images/dns/rs.png)

## Kết quả

Sau khi cấu hình xong, tiến hành `reboot` lại hệ thống và kiểm tra

#### Check file conf, zone 

> ![](../images/dns/check.png)

#### Check server `nslookup ze9hyrus.com`

> ![](../images/dns/result.png)

## Cấu hình slave server

#### /etc/bind/named.conf [làm tương tự master server](#1)

#### `vi /etc/bind/named.conf.local`

> ![](../images/dns/conflocal1.png)

#### `vi /etc/network/interfaces`
Đảm bảo rằng hệ thống đã cài đặt package *ifupdown* trước khi gõ lệnh này

> ![](../images/dns/itf1.png)

#### `vi /etc/resolv.conf `

> ![](../images/dns/rs1.png)

## Kiểm tra

Sau khi cấu hình xong, tiến hành `reboot` lại hệ thống và kiểm tra

#### Check server `nslookup ze9hyrus.com`

> ![](../images/dns/result1.png)
