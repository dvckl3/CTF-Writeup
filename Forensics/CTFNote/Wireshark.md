# Wireshark-Note
Note lại một số filter và các dòng lệnh hay dùng trong wireshark/tshark
Bài viết dùng để tổng hợp các kinh nghiệm có được thông qua quá trình chơi CTF mảng Forensic

Note: Nội dụng được trình bày dưới đây sẽ được viết dưới dạng lý thuyết xen kẽ với các kĩ thuật để mọi người tiện theo dõi hơn. 

(Có thể sẽ có nhiều sai sót về mặt nội dung chuyên môn, hi vọng được mọi người góp ý)

Cheatsheet sưu tầm: 
1. [Wireshark Cheat sheet](https://www.stationx.net/wireshark-cheat-sheet/)
2. [Wireshark User's Guide](https://www.wireshark.org/download/docs/Wireshark%20User%27s%20Guide.pdf)
## Note một số kĩ thuật với Wireshark
### Extract files với Wireshark

![image](https://github.com/user-attachments/assets/d3a55160-0d70-47c0-8c5b-58a182fc7a5d)

Đầu tiên để tải về máy các file được truyền qua giao thức FTP ta click chọn `file` sau đó chọn `Export Objects` và chọn mục `FTP-DATA`,`HTTP` hoặc `TFTP`. 

![image](https://github.com/user-attachments/assets/04cc9404-3e54-46f0-b5d3-13c19ba4c98b)

Sau đó ta click chọn `save all` và `export` ra thư mục đích mà ta muốn.


Lưu ý thêm ta cần chỉnh setting như dưới đây: 

![image](https://github.com/user-attachments/assets/2468baca-c87d-49ef-bec8-b585b1ec5a0b)
Cấu hình này trong Wireshark có ý là ta cho phép Wireshark tái tạo lại (reassemble) luồng dữ liệu TCP từ các gói tin riêng lẻ trước khi `export`




## Note một số kiến thức về mạng

### Giao thức FTP
FTP là viết tắt của File Transfer Protocol , là một trong những giao thức Internet trên tầng ứng dụng. Nó được sử dụng để lưu trữ và trao đổi file. 
Giống như hầu hết các giao thức TCP/IP, FTP dựa trên mô hình Client/Server. Tuy nhiên điểm khác biệt là FTP sử dụng đến 2 kết nối TCP:

- Đầu tiên là Control Connection (sử dụng port 21 trên server): Đây là kết nối TCP logic chính được tạo ra khi phiên làm việc được thiết lập. Nó được thực hiện giữa các quá trình điều khiển và chỉ cho các thông tin điều khiển đi qua như lệnh hay response(phản hồi)
- Data connection (sử dụng port 20 – trên server): Kết nối này sử dụng các quy tắc rất phức tạp vì các loại dữ liệu có thể khác nhau. Nó được thực hiện giữa các quá trình truyền dữ liệu. Kết nối này mở khi có lệnh chuyển tệp và đóng khi tệp truyền xong.


Mô hình FTP được miêu tả như dưới đây: 
![image](https://github.com/user-attachments/assets/a410b49a-c88b-4f97-934a-e9fd7dffcdc8)

Một số lệnh Command thường hay được sử dụng trên FTP

| Command | Đối số (Argument) | Mô tả (Description)                          |
|---------|--------------------|----------------------------------------------|
| USER    | username          | Username                                     |
| PASS    | password          | Password                                     |
| ACCT    | account info      | User account                                 |
| CWD     | pathname          | Thay đổi thư mục làm việc                    |
| CDUP    | none              | Thay đổi thư mục cha                         |
| SMNT    | pathname          | Kết cấu                                      |
| REIN    | none              | Dừng và khởi động lại                        |
| QUIT    | none              | Đăng xuất khỏi FTP                           |
| RETR    | pathname          | Lấy tập tin từ máy chủ                       |
| STOR    | pathname          | Lưu trữ dữ liệu trên máy chủ                 |
| RNFR    | pathname          | Đổi tên từ …                                 |
| RNTO    | pathname          | Đổi tên thành …                              |
| DELE    | pathname          | Xóa file                                     |
| RMD     | pathname          | Xóa thư mục                                  |
| MKD     | pathname          | Tạo thư mục                                  |
| LIST    | pathname          | Liệt kê tệp tin hoặc văn bản                 |
| STAT    | pathname          | Status                                       |
| HELP    | subject           | Hiện màn hình trợ giúp                       |
| PORT    | host-port         | Chỉ định cổng vận chuyển (không mặc định)    |
| TYPE    | type code         | Kiểu vận chuyển (ASCII, image,…)             |
| MODE    | mode code         | Chế độ truyền (stream, block,…)              |

