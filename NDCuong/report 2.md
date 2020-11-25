## SYSTEM STARTUP AND SHUTDOWN
1. [System startup process](#startup)
2. The startup script framework
3. Managing services using

#### System startup process <a name="startup"></a>
Quá trình khởi động của hệ điều hành Linux từ lúc mở máy đến khi hiển thị màn hình đăng nhập bao gồm 8 bước:
- BIOS
- MBR (Master Boot Record)
- Boot loader (GRUB)
- Kernel
- Initial RAM disk - initframs image
- Init (parent process)
- Command Shell using getty
- Graphical User Interface
***
Quy trình chi tiết các bước:
  - **BIOS**:là một phần mềm được cài đặt sẵn (embedded) vào các chíp PROM, EPROM hay bộ nhớ flash nằm trên bo mạch chủ, 
  được chạy đầu tiên khi bạn nhấn nút nguồn hoặc nút reset trên máy tính. Chương trình 
  POST (Power-on Self-test) được thực hiện đầu tiên
