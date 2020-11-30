# PROCESS MONITORING AND SCHEDULING
## [1. Monitoring processes](#mp)
## [2. Shared libraries](#sl)
## [3. Scheduling processes with cron](#sp)
## [4. Crontab command options](#cron)


## 1. Monitoring processes <a name="mp"></a>
###  1.1. Process
> Trong khoa học máy tính, *process*(tiến trình) là một thực thể (instance) của một chương trình máy tính đang được thực thi bởi một hoặc nhiều luồng. Một *process* có riêng một không gian địa chỉ, có ngăn xếp (stack) riêng rẽ, có bảng chứa các đặc tả tập tin (file descriptor) được mở cùng *process* và đặc biệt là có một định danh **PID** (process identifier) duy nhất trong toàn bộ hệ thống vào thời điểm *process* đang chạy

Một số *process* có thể được liên kết với cùng một chương trình. Ví dụ, mở một số phiên bản của cùng một chương trình thường dẫn đến nhiều hơn một *process* được thực thi.

> Một phần quan trọng của việc chạy và quản trị hệ thống Ubuntu liên quan đến việc theo dõi tình trạng tổng thể của hệ thống về bộ nhớ, lưu trữ và sử dụng bộ xử lý. Điều này bao gồm việc biết cách kiểm tra và quản lý cả hệ thống và *process* đang chạy (trong nền)

The Linux terminal có một số lệnh hữu ích có thể hiển thị các *process* đang chạy, hủy chúng và thay đổi mức độ ưu tiên của chúng.

### 1.2. Monitoring processes
- Hiển thị

  - Ps
  
Command `ps` in ra các process đang chạy tại thời điểm thực thi, gồm PID, thời gian đã chạy và tên process, có 1 số option như:

`ps -A` liệt kê tất cả các process đang chạy

`ps -A | grep [process]` hiển thị các process có tên chứa [process]

  - Ps tree
  
`pstree` hiển thị các process dạng cây: các process sẽ hiển thị bên dưới trình quản lý tạo ra chúng

  - Top
  
Command `top` là lệnh hiển thị các process đang chạy trong thời gian thực, hiển thị lượng tài nguyên các process sử dụng và được sắp xếp theo mức độ sử dụng CPU


Để thoát ra, sử dụng interrupt keyboard(phím tắt này thường ngắt(kill) các process đang chạy trong terminal) `Ctrl + C`

  - Htop
  
Htop tương tự như lệnh top, nhưng được bổ sung các chức năng để sắp xếp, thay đổi mức độ ưu tiên hay kill process (v.v.). Htop cũng có giao diện dễ nhìn hơn top. Tuy nhiên htop thường k được cài đặt sẵn trong hệ điều hành và cần được cài đặt với lệnh > apt install htop

- Quản lý
  - Kill
  
  `kill [PID]` sẽ kill process dựa vào PID được cung cấp. PID có thể lấy bằng các lệnh hiển thị (ps, top) hoặc lệnh pgrep
  
  - Pgrep
  
  `pgrep [process]` trả về PID của [process], có thể sử dụng như 1 biến trong lệnh kill: `kill $(pgrep [process])
  
  - Pkill & killall
  Tương tự như kill, pkill & killall ngắt các process nhưng dựa theo tên, ví dụ: `pkill sshd` hoặc `killall sshd`
  
  - Renice
  `renice [priority] [PID]` là lệnh đặt thứ tự ưu tiên cho process có PID được cung cấp, với priority từ -19 đến 19 (19 có mức độ ưu tiên thấp nhất, mặc định là 0)

  - 
## 2. Shared libraries <a name="sl"></a>
## 3. Scheduling processes with cron <a name="sp"></a>
## 4. Crontab command options <a name="cron"></a>
