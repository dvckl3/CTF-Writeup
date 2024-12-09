# UnforgottenBits
> Nguồn: PicoCTF

![image](https://hackmd.io/_uploads/Bk0qM8W4Jg.png)

Chall cho ta một file disk. Đầu tiên ta thử tải file này lên trên Autopsy để phân tích. 

Xem thêm về cách sử dụng tool này tại [đây](https://www.sleuthkit.org/autopsy/help/grep.html)

Sau khi chạy Autopsy thì đây là giao diện làm việc: ![image](https://hackmd.io/_uploads/S1iW5CWEyg.png)

Đọc thông tin thì ta thấy file disk gồm có 3 volumes bình thường và 1 unallocated volume, đại loại thì đây là các phân vùng trên ổ đĩa vật lí. Nói thêm một chút về unallocated space thì đây là vùng dữ liệu chưa được gán cho bất cứ phân vùng hay volume nào và hệ điều hành không sử dụng nó nữa. Trong các vùng này thường là các dữ liệu đã bị xóa hoặc đã bị ghi đè lên. 
Ta bỏ qua volume này và đi khảo sát các volume còn lại. 

![image](https://hackmd.io/_uploads/BJjZzJfNJl.png)

Volume 2 cũng không có gì quan trọng.

Volume 3 thì chứa một file đã bị xóa. Đây là một swap partition. Thông tin về nó mọi người có thể xem tại đây [Linux swap / Solaris](https://forums.gentoo.org/viewtopic-t-481092-start-0.html)
Đại loại thì đây là phân vùng swap , sử dụng làm bộ nhớ ảo cho hệ thống Linux hoặc Solaris. 
Ngoài ra ta có thể đọc và phân tích thông tin về các phân vùng trên thông qua lệnh `fdisk` như dưới đây 

![image](https://hackmd.io/_uploads/B1B_VJzNyg.png)

Dấu * ở phân vùng `disk.flag.img1` cho ta biết đây là phân vùng khởi động, có thể chứa bootloader. Bootloader là một chương trình nhỏ có chức năng load hệ điều hành vào trong bộ nhớ. 

Khi đọc thông tin về file disk ta cần chú ý thêm cột id. Ở đây ta thấy phân vùng swap có partition identifier là 82. 
Ta tìm thông tin về các partition identifier tại đây : [Wiki](https://en.wikipedia.org/wiki/Partition_type)

Như vậy các phân vùng có id 83 có nghĩa rằng đây là các File system của linux và ta chỉ cần tập trung phân tích 2 phân vùng 1 và 3 ở bảng trên. Phân vùng 1 như ta đã mở ở trên Autopsy thì không có thông tin gì đáng chú ý cho nên bây giờ ta chỉ cần tập trung vào tìm kiếm các thông tin quan trọng trong phần vùng 3 tức là volume 4.

Truy cập vào trong directory home thì ta thấy có một đường dẫn đến các thư mục của user `yone`.

![image](https://hackmd.io/_uploads/HJnyvJz4yg.png)

Có tổng cộng 5 thư mục và một file `.ash_history`. File này không có nội dung gì đáng ngờ nên ta sẽ tiếp tục tìm kiếm trong 5 thư mục còn lại.
![image](https://hackmd.io/_uploads/HJq4PJGNke.png)

Đầu tiên là thư mục `.lynx`. Ta thấy ở bên trong có một file log
![image](https://hackmd.io/_uploads/SJztDJf4yl.png)

Đây là nội dung của file log đó: 

```
www.google.com
https://www.google.com/search?q=number+encodings&source=hp&ei=WeC9Y77KJ_iwqtsP0sGu6A0&iflsig=AK50M_UAAAAAY73uaRxDkbHRUH8jn4OVhOgM8riUqvVI&ved=0ahUKEwj-2r_EgL78AhV4mGoFHdKgC90Q4dUDCAk&uact=5&oq=number+encodings&gs_lcp=Cgdnd3Mtd2l6EAMyBggAEBYQHjIFCAAQhgMyBQgAEIYDMgUIABCGAzIFCAAQhgM6DgguEIAEELEDEIMBENQCOgsIABCABBCxAxCDAToRCC4QgAQQsQMQgwEQxwEQ0QM6CAgAELEDEIMBOgsILhCABBCxAxCDAToFCAAQgAQ6CAgAEIAEELEDOggILhCABBDUAjoHCAAQgAQQCjoHCC4QgAQQClAAWI0VYPAXaABwAHgDgAHDA4gB-iKSAQkwLjMuNS40LjOYAQCgAQE&sclient=gws-wiz
https://en.wikipedia.org/wiki/Church_encoding
https://cs.lmu.edu/~ray/notes/numenc/
https://www.wikiwand.com/en/Golden_ratio_base
```
Đọc sơ qua thì ta thấy 3 đường dẫn cho ta thông tin về 3 loại mã khóa khác nhau và có vẻ cả 3 không liên quan tới nhau lắm. Tạm thời ta chưa cần quan tâm tới chi tiết này.

Thư mục gallery cho ta xem một vài tấm ảnh và cũng không thu được thông tin gì đáng kể. 

![image](https://hackmd.io/_uploads/H1D8uJzVke.png)

Nhưng ở thư mục irclogs ta thấy có vài thứ trông khá sú.

![image](https://hackmd.io/_uploads/SJn2d1fEJg.png)

Có 3 thư mục con chứa các file log. Ta sẽ mở chúng lên và đọc xem thông tin bên trong đó. 

```
[08:12] <yone786> Ok, let me give you the keys for the light.
[08:12] <avidreader13> I’m ready.
[08:15] <yone786> First it’s steghide.
[08:15] <yone786> Use password: akalibardzyratrundle
[08:16] <avidreader13> Huh, is that a different language?
[08:18] <yone786> Not really, don’t worry about it.
[08:18] <yone786> The next is the encryption. Use openssl, AES, cbc.
[08:19] <yone786> salt=0f3fa17eeacd53a9 key=58593a7522257f2a95cce9a68886ff78546784ad7db4473dbd91aecd9eefd508 iv=7a12fd4dc1898efcd997a1b9496e7591
[08:19] <avidreader13> Damn! Ever heard of passphrases?
[08:19] <yone786> Don’t trust em. I seed my crypto keys with uuids.
[08:20] <avidreader13> Ok, I get it, you’re paranoid.
[08:20] <avidreader13> But I have no idea if that would work.
[08:21] <yone786> Haha, I’m not paranoid. I know you’re not a good hacker dude.
[08:21] <avidreader13> Is there a better way?
[08:22] * yone786 yawns.
[08:24] <yone786> You’re ok at hacking. I’m good at writing code and using it
[08:24] <avidreader13> What language are you writing in?
[08:26] <yone786> C
[08:26] <avidreader13> Oh, I see.
[08:26] <yone786> I’m glad you like it. I’m sure you wouldn’t understand half of what I was doing.
[08:28] <avidreader13> I understand enough, but I do wish you wouldn’t take so much time with it.
[08:28] <yone786> Sorry. Well, I wish you could learn some things.
[08:29] <avidreader13> But it’s an incredible amount of time you spend on it.
[08:29] <yone786> Haha, don’t take it like that.
```

Đoạn hội thoại của hai người có nhắc tới steghide? Phải chăng là các tấm ảnh lúc nãy trong file gallery. 
