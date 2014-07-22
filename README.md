# Xác thực SSH bằng Keypair

**MỤC LỤC** 
được tạo bằng [DocToc](http://doctoc.herokuapp.com/)

# I. SSH là gì?

SSH là chương trình giúp các thiêt bị có thể xác thực và bảo mật thông tin truyền tải qua nhau. 
Nó được thiết kế để một máy cient có thể đăng nhập và thực thi câu lệnh trên một máy khác, cũng như di chuyển file giữa hai máy.

# II. Các phương thức xác thực bằng SSH

SSH hỗ trợ nhiều phương thức xác thực và để tăng tính bảo mật, bảo mật dựa trên khóa thường đươc sử dụng nhiều hơn so với việc dùng password, 
giúp người dùng có thể đăng nhập vào các máy khác mà không cần phải ghi nhớ quá nhiều mật khẩu. Với cớ chế này, SSH sẽ sinh ra các 
khóa private và public key, public key là khóa công khai sẽ đặt ở máy remote trong khi private key là khóa bí mật được giữ ở máy client.

# III. Các thuật toán để tạo khóa trong SSH

Digital Signature Algorithm (DSA) được phát triển bởi U.S. National Security Agency (NSA) và được phổ biến bởi U.S. National Institute of Standards and Technology (NIST), DSA chỉ có thể sử dụng cho chữ ký
điện tử và không thể sử dụng để mã hóa. 
Rivest-Shamir-Adleman (RSA) là thuật toán mã hóa bất đối xứng được sử dung phổ biến hiện nay, có thể đươc sử dung phổ biến cho mã hóa và chữ ký điện tử.

# IV. Cách cấu hình một cặp khóa bất đối xứng trong Linux

## 1. Chuẩn bị:
Ta có 2 máy là client machine và remote machine
	[user_name@local_host ~]$
	[user_name@remote_host user_name]$
	
## 2. Tạo cặp khóa bất đối xứng trên máy client:
	[user_name@local_host ~]$ ssh-keygen -t rsa -b 2048
- -t: lựa chọn sử dụng khóa rsa1, rsa2 hoặc dsa
- -b: số bit của khóa được tạo, nhỏ nhất là 512 bits và mặc định là 1024 bits.

## 3. Nhập đường dẫn lưu key:
	Enter file in which to save the key (/home/user_name/.ssh/id_rsa): [press enter]
	
## 4. Tạo passphrase cho private key:
Passphrase này để bảo vệ private key của người dùng trong trường hợp bị lộ
```sh	
Enter passphrase (empty for no passphrase): [press enter]
Enter same passphrase again: [press enter]
```
## 5. Đẩy public key qua máy remote
Sau khi hoàn thành các bước tạo key, ta sẽ có 2 khóa được tạo ra (VD; id_rsa và id_rsa.pub), ta chuyển khóa .pub sang máy remote	
	[user_name@local_host ~]$ scp /home/user_name/.ssh/id_rsa.pub remote_host:/home/user_name/
	
## 6. Ta đưa khóa public vào file authorized_keys trong thư mục /home/user_name/.ssh trên máy remote, nếu chưa có ta tạo ra file này
	[user_name@remote_host user_name]$ cat /home/username/id_rsa.pub >> /home/username/.ssh/authorized_keys
	
## 7. Thêm hostname của máy client vào file authorized_keys trên máy remote	
	from="local_host" ssh-rsa AAAAB3NzaC1y ... gwWhN/sYw== user_name@local_host 
	
## 8. Phân quyền cho .ssh và authorized_keys trên máy remote
	[user_name@remote_host user_name]$chmod 700 /home/user_name/.ssh
	[user_name@remote_host user_name]$chmod 644 /home/user_name/.ssh/authorized_keys

## 9. Khởi chạy ssh-agent trên máy client
	eval "$(ssh-agent -s)"
	ssh-add /home/user_name/.ssh/id_rsa





