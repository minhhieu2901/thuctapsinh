# I - Quản trị user, group trên Linux  
### User là gì?  
\- User là người có thể truy cập đến hệ thống.  
\- User có username và password.  
\- User có 2 loại: super user và regular user.  
\- Mỗi user đều có 1 giá trị định danh được gọ là UID.  
\- Định danh của người dùng bình thường sử dụng giá trị bắt đầu từ 500.  

### Group là gì?  
\- Group là tập hợp của nhiều user.  
\- Mỗi user luôn là thành viên của 1 group.  
\- Khi tạo 1 user thì mặc định 1 group cũng được tạo ra.  
\- Mỗi group còn có 1 định danh riêng được gọi là GID.  
\- Định danh của group thường sử dụng giá trị từ 500.  

> ## 1.Các file dữ liệu  
### 1.1. `/etc/passwd:` lưu thông tin tài khoản của người dùng  
- Khảo sát thông tin user  
```sh
cat /etc/passwd
```
hoặc  
```sh
cat /etc/passwd | more
```  
> Mỗi user tạo ra được lưu trên 1 dòng.  

- Cấu trúc file: 

<img src = "../../images/25 bai linux/download.png" width = 400 hight = 500>  


    Trong đó:  

    - Username: tên đăng nhập của người dùng, có phân biệt HOA/thường; nên sử dụng chữ thường.  
    - Password: lưu chuỗi password đã mã hóa, nếu có sử dụng /etc/shadow thì ở đây sẽ là chữ x.  
    - User ID: hệ thống dùng user id để phân biệt người này với người khác.  
    - Group ID: nhóm của user này.  
    - Comment: mô tả cho user.   
    - HomeDirectory: thư mục home của từng user.  
    - Shell: tên chương trình sẽ chạy thực thi ngay sau khi user login vào. Nếu không có shell thì user sẽ không thể login vào.  

 
- Khi tạo 1 user thì mặc định tạo ra 1 group cùng tên và chứ user đó.  
- user id = 0 là user root.  
\+ id = 1 - 99 ==> dùng cho system service.  
\+ id = 100 -499 ==> dùng cho system cài thêm.  
\+ id >= 500 ==> dùng cho user và group thường.  

### 1.2. `/etc/shadow:` lưu thông tin mật khẩu của người dùng.  
```sh
cat /etc/shadow
```  
kết quả: 
```sh
root:$6$VPjOJxdySUjgLpFl$ikOFllvIkdiq1kYlpfskE6l9LZzLG9K0893FJHSQ6k53tBf9chpPIgNBgbyRjhrq.00DIaOqjKvwdJhh6OY1m.::0:99999:7:::
```

***Thứ tự thông tin:***   
   \- username  
   \- password đã được mã hóa  
   \- Ngày đổi password gần nhất(1/1/1970 + số ngày đến ngày hôm nay)  
   \- 0(1 ngày sau mới có thể đổi password), 1(không gia hạn đổi password).  

### 1.3. `/etc/default/useradd`  

<img src = "../../images/25 bai linux/useradd.png">

#### **Nội dung file:** 
 
\- `GROUP:` là ID của group mặc định mà user sẽ được đưa vào khi tạo ra.    
\- `HOME:` đường dẫn mặc định chứa các thư mục của từng thành viên khi tạo ra.    
\- `INACTIVE:` Thời gian mà tài khoản sẽ bị khóa vĩnh viễn sau khi mật khẩu user đó hết hạn.  
\- `EXPIRE:` Ngày mà tài khoản của thành viên được tạo ra mặc định sẽ bị khóa.  
\- `SHELL:` hệ vỏ mặc định mà user được sử dụng. Hiểu đơn giản, đây là môi trường giúp người dùng gõ và thực thi các câu lệnh Linux.  
\- `SKEL:` thư mục mẫu, khi user được tạo ra mặc định sẽ copy các file trong đây vào /home/tên-user.  
\- `CREATE_MAIL_SPOOL:` cho phép thành viên được sử dụng mail trên server nếu tùy chọn là yes. Mail sẽ được lưu trong fle /var/mail/tên-user hoặc /var/spool/mail/tên-user.  

### 1.4. `/etc/group`  

<img src = "../../images/25 bai linux/group.png">  

***Cấu trúc:***  
**GroupName : Password : GroupID : User1,User2,…**  
 \- `GroupName:` tên nhóm  
 \- `Password:` lưu chuỗi password, được ghi là x.  
 \- `Group ID:` ID của nhóm.  
 \- `User1, User2,...:` Danh sách các user nằm trong group này, ngăn cách nhau bằng dấu ",".  

> ## 2.Quản lý user  
- Lệnh tạo user mới  
```sh
useradd <tên user>  
[root@localhost ~]#useradd u2
```
Ngay sau khi tạo user, ta cần tạo mật khẩu cho nó:  
```sh
passwd <tên user cần đặt pass>
[root@localhost ~]#passwd u2
``` 
- Tạo user với thư mục tùy chọn  
```sh
useradd -d /<tên thư mục>  <tên user>
[root@localhost ~]# useradd -d /Kithuat u3
```
- Tạo user với group tùy chọn(đã tồn tại và chưa tồn tại)
```sh
useradd -G <tên group> <tên user>
[root@localhost ~]# useradd -G Kithuat a1
```
- Tạo một user không tạo ra thư mục riêng
```sh
useradd -M <tên user>
[root@localhost ~]# useradd -M u4
```
- Tạo user và tự khóa vào ngày nhất định
```sh
useradd -e <năm-tháng-ngày> <tên user>
[root@localhost ~]# useradd -e 2019-8-12 u5
```
- Xóa user
```sh
userdel <Tên user>
[root@localhost ~]# userdel u2
```
- Kiểm tra user u3 đã có trong group
```sh
[root@localhost ~]# grep u3 /etc/passwd
u3:x:1002:1006::/Kithuat:/bin/bash
```


> ## 3. Quản lý group  
`groupadd` - tạo group  
`groupdel` - xóa group  
- Tạo group:
```sh
groupadd [tên group]
```  
- Tạo user và đưa vào group (đã tồn tại và chưa tồn tại): 
```sh
useradd -G [tên group] [tên user]  
```
- Add user vào group đã tồn tại
```sh
usermod -G <tên group> <tên user>
[root@localhost ~]# usermod -G Kithuat u3
```
- List toàn bộ user trong group Kithuat
```sh
[root@localhost ~]# cat /etc/group | grep Kithuat
Kithuat:x:1004:a1,u3
```


# II - File permissions - Quyền truy cập tệp  
- `logout:` logout ra khỏi hệ thống  
- `who:` các tài khoản đang đăng nhập vào hệ thống  
- `whoami:` đang đăng nhập vào hệ thống bằng user nào.  
- `su:` đăng nhập vào bằng user khác mà không cần logout, thoát bằng lệnh "exit" 

> ## 1.Một số khái niệm về quyền truy cập  
### Các quyền truy cập file  
Lệnh `ls -la` hoặc `ls -l` list ra file với đầy đủ thông tin về quyền truy cập  

<img src = "../../images/25 bai linux/ls -l.png">  

Trong đó: 

\+ *`d`* là directory(thư mục), dấu *-* là file bình thường, *l* là link.  
\+ 9 ký hiệu tiếp theo ở  cột đầu: có dạng rwxrwxrwx hoặc rwxr-xr-x là lặp lại 3 lần của rwx (read-write-execute) = quyền đọc, ghi, thực thi đối với file.  
\+ 3 đối tượng sở hữu là: chủ sở hữu(owner), nhóm sở hữu(group owner) và other.  

### Các quyền truy cập thư mục  
- **Read:** Quyền đọc thư mục cho phép người dùng có thể xem các nội dung có trong thư mục, đọc tên các file có trong thư mục.  
- **Write:** Được phép thêm hoặc xóa file trong thư mục.  
- **Execute:** Thực thi lệnh, chương trình. 
*Note:* Người dùng phải có quyền thực thi đối với thư mục `bin` thì mới có thể chạy các lệnh `cd,ls,..`  

> ## 2.Thay đổi quyền truy cập  
- **chmod - thay đổi quyền truy cập trên file**  
  \* Dùng `chmod` với quyền biểu diễn dưới dạng kí tự. 

<img src = "../../images/25 bai linux/chmod.png"> 

Cú pháp: 
```sh
chmod [u/g/o][+/-/=][r,w,x] [tên file]
```  
Ví dụ:  
\- Thêm quyền thực thi file a.txt cho chủ sở hữu:
 ```sh
chmod u+x a.txt  hoặc #chmod u=x a.txt
``` 

\* Dùng `chmod` với quyền biểu diễn dưới dạng số.  


<img src = "../../images/25 bai linux/chmodso.png"> 

Cú pháp:

```sh
chmod <tùy chọn> <chỉsố phân quyền> <tên tập tin/thư mục>
``` 

***Các tùy chọn***:  
`-v:` Hiển thị báo cáo sau khi chạy lệnh,mỗi lần đổi quyền là một lần hiển thị.  
`-c:` Giống như trên nhưng chỉ hiển thị khi nó đã làm xong tất cả.  
`-R:` Áp dụng luôn vào các file/folder trong thư mục được cấp quyền. 

- **chown - thay đổi chủ sở hữu**
Cú pháp:  


```sh
chown <tùy chọn> <user:group> <tên file/thư mục>
```  

***Các tùy chọn:***  
`-v:` Hiển thị báo cáo sau khi chạy lệnh,mỗi lần đổi quyền là một lần hiển thị.  
`-c:` Giống như trên nhưng chỉ hiển thị khi nó đã làm xong tất cả.  
`-R:` Áp dụng luôn vào các file/folder trong thư mục được cấp quyền.   

- **chgrp - thay đổi nhóm sở hữu**  

Cú pháp:  

```sh
chgrp <tùy chọn> <group sở hữu mới> <tên file/thư mục>
```  

# III - Một số câu lệnh khác về mật khẩu  
- Thay đổi mật khẩu của người dùng khác  
```sh
sudo passwd username
```  
- Khóa mật khẩu tài khoản người dùng  
```sh
sudo passwd -l username
```
Option khác:   
        `-u:` mở khóa mật khẩu  
        `-d:` xóa mật khẩu người dùng  

- Đặt ngày hết hạn cho những tài khoản tạm thời
```sh
usermod -e Y/M/D <user>
[root@localhost ~]# usermod -e 2019/11/30 u1
```
- Giới hạn số ngày tối thiểu để thay đổi password
```sh
chage -m <day> <user>
[root@localhost ~]# chage -m 3 u1
```
- Số ngày tối đa để thay đổi password
```sh
chage -M <day> <user>
[root@localhost ~]# chage -M 10 u1
```
- Cài đặt thời gian khóa tài khoản sau khi mật khẩu hết hạn
```sh
chage -I <day> <user>
[root@localhost ~]# chage -I 5 u1
```










## TÀI LIỆU THAM KHẢO  
- [https://support.maxserver.com/](https://support.maxserver.com/009025-H%C6%B0%E1%BB%9Bng-d%E1%BA%ABn-ph%C3%A2n-quy%E1%BB%81n-t%E1%BA%ADp-tin-v%C3%A0-th%C6%B0-m%E1%BB%A5c-tr%C3%AAn-Linux)
- [https://cuongquach.com/](https://cuongquach.com/tao-user-linux-bang-tay.html)
- [https://freetuts.net/](https://freetuts.net/cac-quyen-truy-cap-file-va-folder-tren-linux-425.html)
- [https://support.maxserver.com/](https://support.maxserver.com/767313--H%C6%B0%E1%BB%9Bng-d%E1%BA%ABn-qu%E1%BA%A3n-l%C3%BD-User-v%C3%A0-Group-tr%C3%AAn-Linux)
- [https://sinhvientot.net/](https://sinhvientot.net/huong-dan-quan-tri-user-group-tren-linux/)
- [https://www.slideshare.net/caotu/](https://www.slideshare.net/caotu/ti-liu-hng-dn-qun-l-user-phn-quyn-trong-ubuntu-linux-10b4-fithou)