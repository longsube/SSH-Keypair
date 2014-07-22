# Xác thực SSH bằng Keypair

**MỤC LỤC** 
được tạo bằng [DocToc](http://doctoc.herokuapp.com/)

- [Xác thực SSH bằng Keypair](#user-content-x%C3%A1c-th%E1%BB%B1c-ssh-b%E1%BA%B1ng-keypair)
- [I. SSH là gì?](#user-content-i-ssh-l%C3%A0-g%C3%AC)
- [II. Các phương thức xác thực bằng SSH](#user-content-ii-c%C3%A1c-ph%C6%B0%C6%A1ng-th%E1%BB%A9c-x%C3%A1c-th%E1%BB%B1c-b%E1%BA%B1ng-ssh)
- [III. Các thuật toán để tạo khóa trong SSH](#user-content-iii-c%C3%A1c-thu%E1%BA%ADt-to%C3%A1n-%C4%91%E1%BB%83-t%E1%BA%A1o-kh%C3%B3a-trong-ssh)
- [IV. Cách cấu hình một cặp khóa bất đối xứng trong Linux](#user-content-iv-c%C3%A1ch-c%E1%BA%A5u-h%C3%ACnh-m%E1%BB%99t-c%E1%BA%B7p-kh%C3%B3a-b%E1%BA%A5t-%C4%91%E1%BB%91i-x%E1%BB%A9ng-trong-linux)
	- [1. Chuẩn bị:](#user-content-1-chu%E1%BA%A9n-b%E1%BB%8B)
	- [2. Tạo cặp khóa bất đối xứng trên máy client:](#user-content-2-t%E1%BA%A1o-c%E1%BA%B7p-kh%C3%B3a-b%E1%BA%A5t-%C4%91%E1%BB%91i-x%E1%BB%A9ng-tr%C3%AAn-m%C3%A1y-client)
	- [3. Nhập đường dẫn lưu key:](#user-content-3-nh%E1%BA%ADp-%C4%91%C6%B0%E1%BB%9Dng-d%E1%BA%ABn-l%C6%B0u-key)
	- [4. Tạo passphrase cho private key:](#user-content-4-t%E1%BA%A1o-passphrase-cho-private-key)
	- [5. Đẩy public key qua máy remote](#user-content-5-%C4%90%E1%BA%A9y-public-key-qua-m%C3%A1y-remote)
	- [6. Authorized_keys](#user-content-6-authorized_keys)
	- [7. Phân quyền](#user-content-7-ph%C3%A2n-quy%E1%BB%81n)
	- [8. Khởi chạy](#user-content-8-kh%E1%BB%9Fi-ch%E1%BA%A1y)
	- [9. Kết nối](#user-content-9-k%E1%BA%BFt-n%E1%BB%91i)


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
```sh
[user_name@local_host ~]$
[user_name@remote_host user_name]$
```

## 2. Tạo cặp khóa bất đối xứng trên máy client:
```sh
[user_name@local_host ~]$ ssh-keygen -t rsa -b 2048
```
- -t: lựa chọn sử dụng khóa rsa1, rsa2 hoặc dsa
- -b: số bit của khóa được tạo, nhỏ nhất là 512 bits và mặc định là 1024 bits.

## 3. Nhập đường dẫn lưu key:
```sh
Enter file in which to save the key (/home/user_name/.ssh/id_rsa): [press enter]
```
	
## 4. Tạo passphrase cho private key:
Passphrase này để bảo vệ private key của người dùng trong trường hợp bị lộ
```sh	
Enter passphrase (empty for no passphrase): [press enter]
Enter same passphrase again: [press enter]
```
## 5. Đẩy public key qua máy remote
Sau khi hoàn thành các bước tạo key, ta sẽ có 2 khóa được tạo ra (VD; id_rsa và id_rsa.pub), ta chuyển khóa .pub sang máy remote	
```sh
[user_name@local_host ~]$ scp /home/user_name/.ssh/id_rsa.pub remote_host:/home/user_name/
```
	
## 6. Authorized_keys 
Ta đưa khóa public vào file authorized_keys trong thư mục /home/user_name/.ssh trên máy remote, nếu chưa có ta tạo ra file này
```sh
[user_name@remote_host user_name]$ cat /home/username/id_rsa.pub >> /home/username/.ssh/authorized_keys
```
Thêm hostname của máy client vào file authorized_keys trên máy remote	
```sh
from="local_host" ssh-rsa AAAAB3NzaC1y ... gwWhN/sYw== user_name@local_host 
```
	
## 7. Phân quyền 
Phân quyền cho .ssh và authorized_keys trên máy remote
```sh
[user_name@remote_host user_name]$chmod 700 /home/user_name/.ssh
[user_name@remote_host user_name]$chmod 644 /home/user_name/.ssh/authorized_keys
```

## 8. Khởi chạy 
Khởi chạy ssh-agent trên máy client
```sh
eval "$(ssh-agent -s)"
ssh-add /home/user_name/.ssh/id_rsa
```
	
## 9. Kết nối
Tiến hành SSH tới máy remote sẽ không yêu cầu password
```sh
[user_name@local_host ~]$ssh user_name@local_host
```





