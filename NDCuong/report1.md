#### 1.	cài đặt gói bằng apt, yum và dpkg,rpm
- **apt:** `sudo apt [option(install/remove/update/upgrade)] [package]`
> ![](./images/report1/apt1.png)
> ![](./images/report1/apt2.png)
- **dpkg:**`sudo dpkg [option] [file.deb]`
> ![](./images/report1/dpkg1.png)
- **rpm:**`sudo alien [file.rpm]`(convert to file.deb) 
#### 2.	phân biệt đường đẫn tuyệt đối và đường dẫn tương đối
- **Đường dẫn tuyệt đối:** chỉ chính xác đến file(thư mục), có thể truy cập từ bất cứ đâu trong hệ thống
- **Đường dẫn tuyệt đối:** chỉ các bước truy cập đến file(thư mục) từ một vị trí cụ thể
#### 3.	sử dụng vi, vim để sửa file
- **Tạo(truy cập) file:** `vi [file name]`
- **Chỉnh sửa file:**
> ![](./images/report1/tutorial-vi.png)
#### 4.	add disk, cấu hình lvm
- **add disk:**`lsblk`(kiểm tra ổ cứng mới thêm vào)
> ![](./images/report1/lsblk.png)
- **partition disk:**  `fdisk /dev/[disk]` => option
> ![](./images/report1/partition.png)
- **mount** `mount [option] ... [target] [directory]`
> ![](./images/report1/mount&check.png)
#### 5.	cấu hình ssh, truy cập máy ảo từ ssh client 
- **Khởi động, kiểm tra máy chủ ssh:** 
 + Khởi động 
 > `systemctl start ssh`
 + Kiểm tra trạng thái
 >`systemctl status ssh`
 > ![](./images/report1/startSSH.png)
- **Kết nối đến máy chủ ssh:**
> `ssh -p [port] [username]@[host]`
> ![](./images/report1/ssh1.png)
- **Cấu hình SSH key phía máy chủ:**
  + Tạo key: `ssh-keygen -t rsa` =>Chọn đường dẫn hoặc bỏ qua => nhập passphase hoặc bỏ qua
  > ![](./images/report1/keygen.png)
  + Sửa file ssh_config : `vi /etc/ssh/sshd_config` => nhập 2 dòng PubkeyAuthentication yes và AuthorizedKeysFile .ssh/authorized_keys 
  > ![](./images/report1/sshconfig.png)
  + Đặt file public key vào thư mục /home/[username]/.ssh và rename thành authorized_keys 
  + Gửi file private key cho client
- **Cấu hình SSH key phía client (sử dụng mobaXterm):**
  + Tạo seession ssh, nhập địa chỉ máy chủ và load file private key
  > ![](./images/report1/sshkey.png)
  + Done 🙄
