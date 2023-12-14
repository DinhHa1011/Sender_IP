## Tạo một users check list
- file này chứa user muốn cấu hình whitelist và tên định danh
```
vim /etc/postfix/users
```
```
ndt@hadt.space  spamhater
kth@hadt.space  spamlover
```
## Tạo 2 file chứa ip wl và block
- File ip_checks, wl địa chỉ `123.30.170.251` và block các địa chỉ gửi khác
```
vim /etc/postfix/ip_checks
```
```
123.30.170.251  OK
0.0.0.0/0       REJECT !!!!!!!!!!!!!!!!!!!!!!!1
```
- File ip_checks2, wl địa chỉ `113.185.42.227` và block các địa chỉ gửi khác
```
vim /etc/postfix/ip_checks2
```
```
113.185.42.227  OK
0.0.0.0/0       REJECT IP_Khong_duoc_gui_mail
```
## Config postfix
```
vim /etc/postfix/main.cf
```
- Trong file main thêm những dòng dưới đây
```
mua_client_restrictions = check_sender_access hash:/etc/postfix/users
smtpd_restriction_classes = spamhater, spamlover
spamhater =
        check_client_access cidr:/etc/postfix/ip_checks,
        reject
spamlover =
        check_client_access cidr:/etc/postfix/ip_checks2,
        reject
```
```
vim /etc/postfix/master.cf
```
- Trong file master thêm dòng
```
-o syslog_name=postfix/submission
-o smtpd_client_restrictions=$mua_client_restrictions
```
- Sau khi áp dụng config:
  - ndt@hadt.space áp dụng file config ip_checks
  - kth@hadt.space áp dụng file config ip_checks2
## Hash list
- Run các lệnh dưới đây
```
postmap /etc/postfix/ip_checks
postmap /etc/postfix/ip_checks2
```
## Restart postfix
```
systemctl restart postfix
```
## Test
### Với user ndt@hadt.space
- Với IP `123.30.170.251`
![image](https://github.com/DinhHa1011/Sender_IP/assets/119484840/d0ed4daf-222c-495c-8b3c-15847e2e9732)
- Với IP khác
![image](https://github.com/DinhHa1011/Sender_IP/assets/119484840/59096d89-67e9-49be-aaf3-2024b0ca4dda)
### Tương tự với kth@hadt.space
