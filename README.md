## 1. Mục tiêu bài tập
- Vận hành hệ thống theo nguyên tắc đặc quyền tối thiểu (*Least Privilege*), hạn chế rủi ro khi thao tác trực tiếp bằng tài khoản `root`.
- Cấu hình khóa bảo mật SSH Key cho tài khoản `root` để truy cập máy chủ an toàn không cần mật khẩu.
- Tạo tài khoản làm việc riêng có tên là `devops` trên máy chủ Ubuntu Server để thực hiện các thao tác quản trị hàng ngày.
- Cấp đặc quyền quản trị an toàn cho user `devops` thông qua nhóm `sudo`.
- Sao chép khóa SSH Key từ `root` sang `devops` và phân quyền thư mục chuẩn xác để cho phép tài khoản `devops` đăng nhập được qua SSH.

---

## 2. Phần 1: Cấu hình đăng nhập SSH Key cho tài khoản Root

### Các lệnh thực hiện trên máy cá nhân & VPS:
```bash
# 1. Kiểm tra / lấy chuỗi SSH Public Key trên máy cá nhân
Get-Content ~/.ssh/id_ed25519.pub

# 2. Đăng nhập VPS bằng tài khoản root và mật khẩu
ssh root@221.121.3.124

# 3. Tạo thư mục .ssh và phân quyền trên VPS
mkdir -p ~/.ssh
chmod 700 ~/.ssh

# 4. Lưu Public Key vào file authorized_keys
nano ~/.ssh/authorized_keys

# 5. Phân quyền cho file authorized_keys
chmod 600 ~/.ssh/authorized_keys

```
## 3. Phần 2: Khởi tạo User devops & Thiết lập quyền Sudo
Các lệnh thực hiện trên VPS:
```bash# 1. Cập nhật hệ thống và cài đặt công cụ rsync hỗ trợ sao chép dữ liệu
apt update && apt install -y rsync

# 2. Tạo tài khoản người dùng mới tên 'devops'
adduser devops

# 3. Thêm user 'devops' vào nhóm quản trị sudo
usermod -aG sudo devops

# 4. Sao chép cấu hình SSH Key từ thư mục của root sang thư mục home của user devops
# Đồng thời tự động cập nhật owner/group thành devops:devops và phân quyền an toàn
sudo rsync --archive --chown=devops:devops ~/.ssh /home/devops/
```

## 4. Phần 3: Nhật ký (Log) kiểm tra kết quả thực thi
Thao tác 1: Kiểm tra kết nối SSH bằng tài khoản devops
Thực hiện trên Terminal/PowerShell máy cá nhân:
```bash
PS C:\Users\ADMIN> ssh devops@221.121.3.124
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-63-generic x86_64)

 * Documentation:  [https://help.ubuntu.com](https://help.ubuntu.com)
 * Management:     [https://landscape.canonical.com](https://landscape.canonical.com)
 * Support:        [https://ubuntu.com/pro](https://ubuntu.com/pro)

devops@dat-linux:~$ 



devops@dat-linux:~$ sudo whoami
[sudo] password for devops: 
root
```