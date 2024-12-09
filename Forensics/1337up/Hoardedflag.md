
# Hoarded Flag 
> Nguồn: 1337UP Live CTF 2024

![image](https://hackmd.io/_uploads/SJBFPmZEJx.png)

Tải file tại [đây](https://cybersharing.net/s/7814edce5ba49653)

Đề bài cho ta một tệp zip chứa một file `memory_dump.raw`
![image](https://hackmd.io/_uploads/BkVpPXW41e.png)

Giải thích một chút về file dump: file dump giúp ta ghi lại chi tiết tình hình và những vấn đề mà máy tính của ta đang gặp phải trước thời điểm xảy ra sự cố. Còn raw memory dump là một file chứa toàn bộ dữ liệu được sao chép trực tiếp từ bộ nhớ vật lý (RAM) của một hệ thống tại một thời điểm cụ thể. Định dạng file là .raw tức là dữ liệu thô không qua bất cứ bước xử lí hay định dạng nào. 


Để phân tích được file dump này thì ta cần sử dụng bộ công cụ Volatility.

Mọi người có thể tham khảo thêm ở đây [Volatility Cheat Sheet](https://blog.onfvp.com/post/volatility-cheatsheet/) hoặc [Volatility](https://volatility3.readthedocs.io/en/latest/)

Mình thử chạy lệnh `python3 vol.py -f ...` nhưng vẫn gặp một số lỗi khá khó chịu. Cho nên mình đã tìm lại link github của bộ công cụ và thử một cách chạy khác bằng cách khởi tạo một môi trường ảo python trên ubuntu. Cụ thể để làm được như vậy đầu tiên ta cần tải python3.10-venv về. Module này được sử dụng để tạo và quản lí Virtual Environment. Tải bằng lệnh `sudo apt install python3.10-venv`. Kế đến ta chạy hai lệnh `python3 -m venv venv && . venv/bin/activate` và `pip install -e .[dev]` và set up xong môi trường để làm việc với Volatility.

Tiếp theo ta sẽ đi giải quyết bài tập trên. 
Đầu tiên ta cần xác định phiên bản và kiến trúc của hệ điều hành mà file dump được trích xuất ra. Ta có thể kiểm tra trực tiếp bằng lệnh `file` như sau: 


![image](https://hackmd.io/_uploads/rJcowEWNyg.png)


Như vậy đây là file dump của hệ điều hành windows và ta sẽ sử dụng các plugin windows để xử lí:
`vol -f /home/duc112006/1337/hoardedflag/memory_dump.raw windows.info`

![image](https://hackmd.io/_uploads/rJzI_4bE1x.png)

Tiếp theo ta chạy plugin `windows.pslist` để list ra các tiến trình đang được chạy trong lúc dump file memory
![image](https://hackmd.io/_uploads/rJWqYV-41e.png)

Ta thử đọc lướt qua coi thử có gì đáng ngờ hay không. Chú ý đến tiến trình `cmd.exe`
![image](https://hackmd.io/_uploads/HyQs9Nb4kl.png)

Để chạy và phân tích các lệnh được thực thi trên tiến trình này ta sẽ sử dụng plugin `windows.cmdscan`

![image](https://hackmd.io/_uploads/HyXksE-EJe.png)

Đọc các command history ta thấy đáng ngờ nhất ở các dòng này: 
```
** 1032 conhost.exe     0x23442febbf0   _COMMAND_HISTORY.CommandBucket_Command_0        0x2344310e0c0   cd Desktop
** 1032 conhost.exe     0x23442febbf0   _COMMAND_HISTORY.CommandBucket_Command_1        0x2344310e0e0   7z a -pScaredToDeathScaredToLook1312 -mhe flag.7z flag.zip
** 1032 conhost.exe     0x23442febbf0   _COMMAND_HISTORY.CommandBucket_Command_70       0x2344310e980   糘
** 1032 conhost.exe     0x23442febbf0   _COMMAND_HISTORY.CommandBucket_Command_88       0x2344310ebc0   糘
** 1032 conhost.exe     0x23442febbf0   _COMMAND_HISTORY.CommandBucket_Command_116      0x2344310ef40   糘
```
Đầu tiên là lệnh `cd Desktop` thay đổi thư mục làm việc sang Desktop và sau đó sử dụng `7z` - một chương trình nén để nén hai file flag.7z và flag.zip lại với nhau với password được cài đặt với flag `-p` là `ScaredToDeathScaredToLook1312`. Cuối cùng là flag `-mhe` tức là bật chế độ mã hóa tiêu đề tệp làm cho tệp chỉ hiện thị được nội dung sau khi đã nhập mật khẩu. Như vậy mục tiêu của ta là tìm ra hai file trên và dùng `7z` để giải nén tìm ra flag. 

Tìm hai file bằng cách gõ lệnh
```
vol -f /home/duc112006/1337/hoardedflag/memory_dump.raw windows.filescan | grep flag
```

![image](https://hackmd.io/_uploads/BJyxkS-NJe.png)

Phần đầu là địa chỉ ảo của file đã được xác định từ đầu. Để dump file ra ta dùng lệnh 
```
vol -f /home/duc112006/1337/hoardedflag/memory_dump.raw windows.dumpfiles.DumpFiles --virtaddr 0xb20dbd74e720
```
Lý do ta dump file flag.7z là vì ở lệnh cmd trên file flag.zip đã được nén vào file đầu ra là flag.7z cho nên ta cần dump lần lượt từng file. 
![image](https://hackmd.io/_uploads/SkVPkSZVkl.png)

Sau đó ta nhập password ở trên vào rồi tiếp tục extract cho tới khi được file `flag.txt`:
![image](https://hackmd.io/_uploads/Hky9ySWVJg.png)

> Flag: INTIGRITI{7h3_m3m0ry_h0ld5_7h3_53cr375}
