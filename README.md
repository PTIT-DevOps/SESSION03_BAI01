# BT01 - Thay đổi cổng kết nối SSH (SSH Port Hardening)

## 1. Kiểm tra cổng SSH hiện tại

```bash
devops@0310263186:~$ sudo ss -tlnp | grep sshd
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=812,fd=3))
LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=812,fd=4))
```

## 2. Mở cổng 2222 trên UFW trước khi đổi cổng

```bash
devops@0310263186:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)

devops@0310263186:~$ sudo ufw allow 2222/tcp
Rule added
Rule added (v6)

devops@0310263186:~$ sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 2222/tcp                   ALLOW IN    Anywhere
[ 3] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 4] 2222/tcp (v6)              ALLOW IN    Anywhere (v6)
```

## 3. Sửa cổng trong /etc/ssh/sshd_config

```bash
devops@0310263186:~$ sudo nano /etc/ssh/sshd_config
devops@0310263186:~$ grep -n "^Port" /etc/ssh/sshd_config
14:Port 2222
```

## 4. Kiểm tra cú pháp và khởi động lại dịch vụ

```bash
devops@0310263186:~$ sudo sshd -t
devops@0310263186:~$ sudo systemctl restart ssh
devops@0310263186:~$ systemctl status ssh --no-pager
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-10-04 14:35:12 UTC; 4s ago
     ...
```

## 5. Kiểm tra kết nối

Từ máy cá nhân, kết nối qua cổng mới:

```bash
thinh@local:~$ ssh -p 2222 devops@160.30.160.103
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.8.0-31-generic x86_64)
Last login: Sun Oct  4 14:36:02 2026 from 115.76.49.152
devops@0310263186:~$
```

Kết nối qua cổng mặc định cũ:

```bash
thinh@local:~$ ssh -p 22 devops@160.30.160.103
ssh: connect to host 160.30.160.103 port 22: Connection timed out
```
