## Tạo 1 file users checklist
- file này chứa các user muốn config wl cùng tên định danh
```
vim /etc/postfix/users
```
```
hadt3@pikab.in  wl1
hadt4@pikab.in  wl2
```
## Tạo 2 file chứa ip wl và block
- file này cho phép ip `123.30.170.250` gửi mail, còn lại thì block 
```
vim /etc/postfix/ip_checks1
``` 
```
123.30.170.250  OK
0.0.0.0/0       REJECT khong_muon_nhan_mail_dau!!!!!!!!!!!!!!!!!
```
- file thứ 2 cho phép ip `123.30.170.250` gửi mail, còn lại thì block
```
123.30.170.251   OK
0.0.0.0/0        REJECT
```
## Config postfix
- Thêm các dòng bên dưới vào main.cf
```
vim /etc/postfix/main.cf
```
```
mua_client_restrictions = check_sender_access hash:/etc/postfix/users
smtpd_restriction_classes = wl1, wl2
wl1 =
        check_client_access cidr:/etc/postfix/ip_checks,
        reject
wl2 =
        check_client_access cidr:/etc/postfix/ip_checks2,
        reject
```
- Thêm dòng bên dưới vào file master.cf
```
-o syslog_name=postfix/submission
-o smtpd_client_restrictions=$mua_client_restrictions,permit_mynetworks,permit_sasl_authenticated,reject
```
- Sau conf trên thì 
	- hadt3@pikab.in nhận conf của ip_checks1 (wl ip `123.30.170.250`)
	- hadt4@pikab.in nhận conf của ip_checks2 (wl ip `123.30.170.251`)
## Hash file
- Chạy các lệnh dưới
``` 
postmap /etc/postfix/users
postmap /etc/postfix/ip_checks1
postmap /etc/postfix/ip_checks2
```
## Restart postfix
```
systemctl restart postfix
```
