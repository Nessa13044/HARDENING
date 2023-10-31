# HARDENING

# 1.Adding users to a predefined admin group
- sudo vidudo
  
![image](https://github.com/Nessa13044/HARDENING/assets/114730329/5787fd67-1230-4b45-92ce-f68f2b99363b)

![image](https://github.com/Nessa13044/HARDENING/assets/114730329/f9538abd-5e6e-419c-a04d-d32fc9432a47)

- ![image](https://github.com/Nessa13044/HARDENING/assets/114730329/280089d4-4afc-4d88-8b4b-e0614bfcb94c)

# 2. Network Configuration and Firewalls
# 2.1. Disable IP Forwarding

Flag net.ipv4.ip_forward được sử dụng để báo cho máy chủ có thể chuyển tiếp gói tin hay không. Nếu máy chủ không dùng để định tuyến thì nên thiết lập giá trị cờ này là 0.

Kiểm tra giá trị của flag net.ipv4.ip_forward

/sbin/sysctl net.ipv4.ip_forward
net.ipv4.ip_forward = 0

Hardening:

Thiết lập thông số cờ net.ipv4.ip_forward trong /etc/sysctl.conf

net.ipv4.ip_forward=0

# 2.2. Disable Send Packet Redirects
- ICMP Redirects được sử dụng để gửi thông tin định tuyến tới một host khác. Khi máy chủ không sử dụng như một bộ định tuyến thì không có nhu cầu chuyển hướng.

Kẻ tấn công có thể sử dụng một máy chủ chiếm được, gửi gói ICMP không hợp lệ để tìm ra mô hình hệ thống mạng của nạn nhân.

Kiểm tra gói tin chuyển hướng có bị vô hiệu hóa:

/sbin/sysctl net.ipv4.conf.all.send_redirects
net.ipv4.conf.all.send_redirects = 0

/sbin/sysctl net.ipv4.conf.default.send_redirects
net.ipv4.conf.default.send_redirects = 0

Nếu các kết quả bằng 0 thì các flag đã được vô hiệu. Nếu các flag khác 0, sửa đổi thông số trong tệp tin sau/etc/sysctl.conf

net.ipv4.conf.all.send_redirects=0

net.ipv4.conf.default.send_redirects=0

Sửa đổi thông số cho phù hợp với Kernel:

 /sbin/sysctl -w net.ipv4.conf.all.send_redirects=0
 /sbin/sysctl -w net.ipv4.conf.default.send_redirects=0
 /sbin/sysctl -w net.ipv4.route.flush=1
 ![image](https://github.com/Nessa13044/HARDENING/assets/114730329/d36f3e2a-3a8d-4d8b-8583-115fba2c9ad8)

 # 2.3 Disable ICMP Redirect Acceptance

Thông điệp ICMP Redirect truyền tải các gói tin định tuyến, giao tiếp với máy chủ (đóng vai trò như bộ định tuyến) để gửi gói dữ liệu thông qua một đường dẫn khác, nó cho phép thiết bị định tuyến bên ngoài cập nhật bảng định tuyến của bạn.

Kẻ tấn công có thể sử dụng ICMP giả mạo để chuyển các email độc hại, làm thay đổi bảng định tuyến…

Thiết lập cờ net.ipv4.conf.all.accept_redirects là 0

Kiểm tra giá trị flag:

/sbin/sysctl net.ipv4.conf.all.accept_redirects
net.ipv4.conf.all.accept_redirects = 0

/sbin/sysctl net.ipv4.conf.default.accept_redirects
net.ipv4.conf.default.accept_redirects = 0

Nếu các kết quả bằng 0, thì flag đã được vô hiệu. Nếu các flag khác 0, sửa đổi thông số trong tệp tin sau /etc/sysctl.conf

net.ipv4.conf.all.accept_redirects=0
net.ipv4.conf.default.accept_redirects=0

Sửa đổi thông số cho phù hợp với Kernel:

/sbin/sysctl -w net.ipv4.conf.all.accept_redirects=0
/sbin/sysctl -w net.ipv4.conf.default.accept_redirects=0
/sbin/sysctl -w net.ipv4.route.flush=1

# 2.4 Disable Secure ICMP Redirect Acceptance

Secure ICMP Redirect tương tự với ICMP Redirect, khác là nó đến từ danh sách các gateway trong danh sách default gateway.

Thiết lập flag net.ipv4.conf.all.secure_redirects là 0.

Kiểm tra giá trị flag:

/sbin/sysctl net.ipv4.conf.all.secure_redirects
net.ipv4.conf.all.secure_redirects = 0

/sbin/sysctl net.ipv4.conf.default.secure_redirects
net.ipv4.conf.default.secure_redirects = 0

nếu các kết quả bằng 0 thì flag đã được vô hiệu hóa. Nếu các flag khác 0, sửa đổi thông số trong tệp tin sau /etc/sysctl.conf

net.ipv4.conf.all.secure_redirects=0
net.ipv4.conf.default.secure_redirects=0

Sửa đổi thông số cho phù hợp với Kernel:

/sbin/sysctl -w net.ipv4.conf.all.secure_redirects=0
/sbin/sysctl -w net.ipv4.conf.default.secure_redirects=0
# 2.5 Enable Ignore Broadcast Requests

Việc chấp nhận ICMP echo và yêu cầu timestamp với đích đến là broadcast hoặc multicast cho mạng của bạn có thể sử dụng để đánh lừa máy chủ tham gia một cuộc tấn công Smurt.

Với cuộc tấn công Smurt, kẻ tấn công sẽ gửi một lượng lớn gói tin ICMP tới địa chỉ nguồn giả mạo, các host nhận được, sẽ gửi lại echo-reply cho địa chỉ giả mạo, làm lưu lượng mạng tăng lên dẫn tới nghẽn hệ thống.

Kiểm tra giá trị flags:

/sbin/sysctl net.ipv4.icmp_echo_ignore_broadcasts
net.ipv4.icmp_echo_ignore_broadcasts = 1

Nếu kết quả trả về bằng 1 thì flag đã được bật. Nếu flag khác 1, sửa đổi thông số trong tệp tin sau /etc/syscrl.conf

net.ipv4.icmp_echo_ignore_broadcasts=1

Sửa đổi thông số phù hợp với Kernel:

/sbin/sysctl -w net.ipv4.icmp_echo_ignore_broadcasts=1
/sbin/sysctl -w net.ipv4.route.flush=1

# 3. SSH Hardening
# 3.1 1. Set SSH Protocol to 2

SSH hỗ trợ 2 giao thức khác nhau và không tương thích với nhau: SSH1, SSH2. SSH1 không còn an toàn. Khuyến nghị sử dụng SSH2.

Xác minh cài đặt SSH chính xác, thực hiện lệnh sau, đầu ra phải là Protocol 2


grep "^Protocol" /etc/ssh/sshd_config

Hardening:

Sửa tệp tin /etc/ssh/sshd_config, bỏ # cho tham số Protocol 2

# 3.2  Set Permissions on /etc/ssh/sshd_config

Tệp tin /etc/ssh/sshd_configchứa cấu hình cho sshd. Tệp này cần được bảo vệ khỏi người dùng không có đặc quyền, nhưng cần cho phép đọc vì nó được sử dụng với nhiều chương trình không có đặc quyền.

Thực hiện lệnh sau để xác định người sử dụng và chủ sở hữu tệp tin sshd_config

/bin/ls -l /etc/ssh/sshd_config
-rw------- 1 root root 762 Sep 23 002 /etc/ssh/sshd_config

Hardening: Nếu người sử dụng và chủ sở hữu không phải root, thực hiện lệnh sau:

chown root:root /etc/ssh/sshd_config

Thiết lập lại quyền:

chmod 600 /etc/ssh/sshd_config

# 3.3  Set SSH IgnoreRhosts to Yes

Tham số IgnoreRhosts xác định tệp tin .rhosts và .shosts sẽ không được sử dụng trong RhostRSAAuthentication hoặc HostbasedAuthenticiation. Đặt tham số này để bắt buộc người dùng phải nhập mật khẩu khi xác thực với SSH.

Kiểm tra tham số:

grep "^IgnoreRhosts" /etc/ssh/sshd_config
IgnoreRhosts yes

Sửa tệp tin /etc/ssh/sshd_config và thiết lập tham số:

IgnoreRhosts yes

# 3.4 Disable SSH Root Login

Tham số PermitRootLogin cho phép người dùng đăng nhập SSH với tài khoản root. Mặc định là không cho phép, điều này bắt buộc quản trị viên phải xác thực bằng tài khoản của họ rồi mới được sang quyền root giúp tránh việc thoái thác trách nhiệm khi xảy ra sự cố.

Kiểm tra tham số:

grep "^PermitRootLogin" /etc/ssh/sshd_config
PermitRootLogin no

Sửa tệp tin /etc/ssh/sshd_config và thiết lập tham số:

PermitRootLogin no
# 3.5 Set SSH PermitEmptyPasswords to No

Tham số PermitEmptyPasswords chỉ định máy chủ cho phép đăng nhập tải khoản với mật khẩu trống. Tắt chức năng này sẽ làm giảm xác suất truy cập trái phép vào hệ thống.

Kiểm tra tham số:

grep "^PermitEmptyPasswords" /etc/ssh/sshd_config
PermitEmptyPasswords no

Sửa tệp tin /etc/ssh/sshd_configvà thiết lập tham số:

PermitEmptyPasswords no


