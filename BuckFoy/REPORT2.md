#Report 2
<a name="mucluc"> </a>
[muc lục ] 

[1.  SYSTEM STARTUP AND SHUTDOWN](#P1)

[1.1. System startup process ](#P11)

<a name="P1"> </a>
# 1. SYSTEM STARTUP AND SHUTDOWN
<a name="P11"> </a>
## 1.1.	System startup process
### 1.1.1.	Linux boot process là gì ?
Linux boot process là quá trình khởi tạo hệ thống Linux. Nó bao gồm từ khi ta bật máy đến khi giao diện người dùng sẵn sàng.
### 1.1.2. Quá trình khởi động hệ thống
Gồm có 8 bước: 

Bước 1. BIOS

Bước 2. Master boot Record (MBR)

Bước 3.  Boot loader
Bước 4. Kernel  được nạp và khởi chạy
Bước 5. các script trong INITRD thực thi
Bước 6. Chương trình init được thực thi
Bước 7. Đăng nhập giao diện đồ họa
Bước 8. Đăng nhập thành công vào hệ thống
Bước 1.	BIOS
BIOS là chương trình chạy đầu tiên khi nhấn nút nguồn hoặc reset lại máy tính.
BIOS thực hiện một công việc gọi là POST (power on self test) kiểm tra các thông số phần cứng của máy tính. Ngoài ra, BIOS cho phép thay đổi cấu hình, thiết lập nó.
BIOS được lưu trữ trên ROM của bo mạch chủ.
Quá trình POST kết thúc thành công, BIOS sẽ tìm kiếm và   khởi  chạy một hệ điều hành trong các thiết bị lưu trữ như ổ cứng . ..
Hệ điều hành Linux được cài trên ổ cứng thì BIOS sẽ tìm kiếm đến MBR.
Bước 2. Master Boot Record
	Sau khi BIOS xác định được thiết bị lưu trữ thì BIOS sẽ đọc trong MBR hoặc phân vùng EFI của thiết bị để nạp vào bộ nhớ  một chương trình. Chương trình này sẽ định vị và khởi động boot loader.
	Đến giai đoạn này, máy tính sẽ không truy cập vào phương tiện lưu trữ. Thông tin về ngày tháng, thời gian và các thiết bị quan trọng nhất được nạp từ CMOS

![hinh11](https://user-images.githubusercontent.com/74639473/100110342-69860280-2e9f-11eb-91aa-468d7f0e9291.png)
![hinh12](https://user-images.githubusercontent.com/74639473/100110366-6e4ab680-2e9f-11eb-897c-cfbf8cbafd3b.png)
![hinh13](https://user-images.githubusercontent.com/74639473/100110375-70147a00-2e9f-11eb-8c81-0b3d7367f3f7.png)
![hinh 14](https://user-images.githubusercontent.com/74639473/100110399-773b8800-2e9f-11eb-8dee-7721946ac4bd.png)
![hinh 15](https://user-images.githubusercontent.com/74639473/100110420-7d316900-2e9f-11eb-8ca0-6deaf2ea8a87.png)
![hinh16](https://user-images.githubusercontent.com/74639473/100110435-7f93c300-2e9f-11eb-9cf3-ff832ed69642.png)
![hinh 17](https://user-images.githubusercontent.com/74639473/100110447-84587700-2e9f-11eb-9c9d-40470ab6e0b0.png)
![hinh 18](https://user-images.githubusercontent.com/74639473/100110457-87536780-2e9f-11eb-8541-1dd8f2654a76.png)
![hinh 19](https://user-images.githubusercontent.com/74639473/100110465-8b7f8500-2e9f-11eb-9a1c-78f7bf841666.png)
![hinh 110](https://user-images.githubusercontent.com/74639473/100110470-8d494880-2e9f-11eb-8d2c-ee772476b64f.png)
