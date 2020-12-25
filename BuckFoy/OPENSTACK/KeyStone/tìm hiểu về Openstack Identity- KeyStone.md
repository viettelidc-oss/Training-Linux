# Tìm hiểu về keystone

## Mục lục

[1. Tổng quan về keystone](#p1)

## 1. Tổng quan về  keystone

Keystone cung cấp một danh mục các user được ánh xạ tới các dịch vụ openstack để người dùng có thể truy  cập. Openstacj Identity hoạt động như một hệ thống xác thực chung cho toaanf bộ hệ thống và có thể tích hợp với các dịch vụ LPAP,PAM.

### 1.1 Chức năng

####  1.1. Identity 

- nhận diện những người dùng đang truy cập đến các  tài nguyên trên cloud
- trong keystone, identity thường được hiểu là User
- xác  thực về chứng chỉ , dữ liệu của User, Group, Projects, Domain, Roles.

#### 1.2. Authentication

- là quá trình xác thực những thông tin để nhận định uẻ
- 