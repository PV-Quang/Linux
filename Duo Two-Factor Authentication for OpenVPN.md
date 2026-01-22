## 1. HƯỚNG DẪN CẤU HÌNH 2FA CHO OPENVPN DÙNG DUO

> Link hướng dẫn cài đặt từ trang chủ Duo: https://duo.com/docs/openvpn

> Điều kiện tiên quyết
- Đã cài sẵn OpenVPN Server
- Đã có tài khoản quản trị Duo (Admin Panel)

### Tích hợp OpenVPN Application vào Duo
Applications → Application Catalog -> OpenVPN -> Add
![1](Image/1.png)
<img width="1746" height="858" alt="Image" src="https://github.com/user-attachments/assets/33de23c7-d03f-47eb-99dc-bfd6ec3e6e3b" />

### Tạo User
Username được tạo trên Duo phải giống Username trên OenVPN Server
<p>
Users -> Users -> Add User -> Nhập thông tin User
<p>
![2](Image/2.png)
<img width="1729" height="805" alt="Image" src="https://github.com/user-attachments/assets/3b82dae1-f75a-4faa-b6ba-ba32b3447841" />

Đối với trường hợp bạn đã từng cài Duo Mobile và đã kích hoạt sđt + app thì khi add user mới rồi gắn cùng số điện thoại, Duo sẽ tự nhận biết thiết bị đã được trust rồi, không cần kích hoạt nữa
<p>
Đối với trường hợp thiết bị chưa từng kết nối với Duo tiến hành kích hoạt như sau

> Edit User và nhấn Send email
<img width="1909" height="833" alt="Image" src="https://github.com/user-attachments/assets/4682c1dd-1d8c-4139-a069-7ef4d71d5067" />
![3](Image/3.png)
<p>

> Mail kích hoạt sẽ được gửi đến mail đã nhập trước đó, nhấn vào link trên mail để active kết nối Duo với thiết bị và setup theo hướng dẫn.
<img width="1513" height="769" alt="Image" src="https://github.com/user-attachments/assets/a6012b63-1935-47ba-becb-9f1bc5635b71" />
![4](Image/4.png)

### Build và install Plugin Duo trên OpenVPN Server
> Cài đặt các gói cần thiết
``` shell
apt install -y build-essential libssl-dev python3 curl
```
![5](Image/5.png)
<img width="1119" height="179" alt="Image" src="https://github.com/user-attachments/assets/504a49c0-7ec0-4ffb-a10b-0b317e36c4f8" />

> Download package Duo_openvpn sau đó tiến hành giải nén và cài đặt
``` shell
wget https://github.com/duosecurity/duo_openvpn/archive/refs/tags/3.0.tar.gz
tar xzf 3.0.tar.gz
cd duo_openvpn-3.0
make && sudo make install
```
![6](Image/6.png)
<img width="1187" height="508" alt="Image" src="https://github.com/user-attachments/assets/70491292-d481-401b-bd64-8254f9afc7c7" />

### Cấu hình thêm Plugin Duo vào OpenVPN Server
> Chỉnh sửa file cấu hình VPN /etc/openvpn/server.conf và thêm 2 dòng sau vào
``` shell
plugin /opt/duo/duo_openvpn.so "IKEY SKEY API_HOST"
reneg-sec 0
```

> Lưu ý thay thông tin "IKEY SKEY API_HOST" bằng thông tin xác thực của OpenVPN Application trên Duo và restart service OpenVPN
``` shell
systemctl restart openvpn@server
```
![7](Image/7.png)
<img width="1651" height="878" alt="Image" src="https://github.com/user-attachments/assets/17b0052a-ea79-4c5c-9cd4-37de8a45d6b2" />
![8](Image/8.png)
<img width="1025" height="592" alt="Image" src="https://github.com/user-attachments/assets/e085b1f4-14c0-479d-af64-65e43f538229" />

> Thêm 2 dòng sau vào Client Profile để active xác thực 2FA cho user
``` shell
auth-user-pass
reneg-sec 0
```
![9](Image/9.png)
<img width="578" height="685" alt="Image" src="https://github.com/user-attachments/assets/f03c71aa-5128-4d22-a48d-8dcd78d53e15" />

> Có thể thêm vào Template luôn để khi chạy Scipt thì sẽ tự sinh Profile có thông tin này luôn không cần chỉnh tay
![10](Image/10.png)
<img width="502" height="411" alt="Image" src="https://github.com/user-attachments/assets/f16fde8c-b4e5-45bc-a13a-6d5d485414e6" />

> Lệnh để xem log VPN dùng khi cần kiểm tra lỗi
``` shell
journalctl -u openvpn@server -f
```

Import profile vào app OpenVPN Connect
 - Username: nhập bất kỳ không cần đúng user VPN đã tạo
 - Password: nhập push để đẩy thông báo về điện thoại hoặc nhập code sinh ra từ app Duo Mobile
 - Private Key Password: nhập password khi tạo user vpn (đối với user tạo không password thì không có ô này)
![11](Image/11.png)
<img width="402" height="676" alt="Image" src="https://github.com/user-attachments/assets/831a6395-74f8-4616-95f2-4790ca29cf03" />
![12](Image/12.png)
<img width="394" height="674" alt="Image" src="https://github.com/user-attachments/assets/a408292d-2519-4ab8-8ce5-6ed07e9ed2ef" />
