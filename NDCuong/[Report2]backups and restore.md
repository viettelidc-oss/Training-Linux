# BACKUP AND RESTORE
### [1.Archiving with tar](#1)
### [2.Using the dd command](#2) 
### [3.Mirroring data between systems: rsync](#3)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 1.Archiving with tar <a name="1"></a>
#### 1.1. Tar
   > Tar trong linux(viết tắt của *tape archive*) là lệnh được sử dụng rộng rãi nhất trên Linux để tạo các tệp lưu trữ nén và có thể được di chuyển dễ dàng từ đĩa này sang đĩa khác hoặc máy này sang máy khác. Lệnh tar có thể nén(và giải nén) các tệp, thư mục thành tệp tin nén có độ nén cao được gọi là tarball. Các định dạng nén tar có thể thực hiện là .tar, .tar.gz và .tar.bz2.
   
#### 1.2. Archiving with tar : `tar [option] [target] [directory]`
   - Create tar Archive File (file .tar): tạo file nén tar trong thư mục hiện tại `tar -cvf example.tar /home/example/` 
      - c: Tạo mới một file nén .tar
      - v: Hiển thị quá trình nén
      - f: Loại file nén
   - Create tar.gz Archive File `tar cvzf example.tar.gz /home/example` hoặc `tar cvzf example.tgz /home/example`
      - z: Nén dạng gzip
   - Create tar.bz2 Archive File `tar cvfj example.bz2 /home/example` hoặc `tar cvfj example.tbz /home/example` hoặc `tar cvfj example.tb2 /home/example`
      - j: Nén dạng bz2
> 
## 2.Using the dd command <a name="2"></a>
## 3.Mirroring data between systems: rsync <a name="3"></a>
