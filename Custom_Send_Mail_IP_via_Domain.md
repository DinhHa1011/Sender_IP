
# [Postfix]Allow/Block_Send_Mail_IP_via_Domain
## Create Domains Check List 
- /etc/postfix/domainf
```
vim /etc/postfix/domainf 
```
```
hadt.space      spamhater
dinhha.online   spamlover

```
- Hash the list
```
postmap /etc/postfix/domainf  
```
## Create IPs Check List 
- /etc/postfix/client_checks (hadt.space)
```
vim /etc/postfix/client_checks 
```
```
#Allow
113.160.0.14   OK
#Block all
0.0.0.0/0       REJECT Dia Chi IP Khong Duoc Phep Gui Mail
```
- /etc/postfix/client_checks_2 (dinhha.online)
```
vim /etc/postfix/client_checks_2
``` 
```
#Allow (khong cho phep dia chi nao gui mail)
#123.30.170.251   OK
#Block all
0.0.0.0/0       REJECT Dia Chi IP Khong Duoc Phep Gui Mail

```
- Hash the list
```
postmap /etc/postfix/client_checks  
postmap /etc/postfix/client_checks_2
```
## Config Postfix 
- /etc/postfix/main.cf 
```
vim /etc/postfix/main.cf 
```
```

mua_client_restrictions = check_sender_access hash:/etc/postfix/domainf
smtpd_restriction_classes = spamhater, spamlover
spamhater =
        check_client_access cidr:/etc/postfix/client_checks,
        reject
spamlover =
        check_client_access cidr:/etc/postfix/client_checks_2,
        reject
```
- /etc/postfix/master.cf
* In `submission inet n       -       y       -       -       smtpd` 
* Add
```
  -o smtpd_client_restrictions=$mua_client_restrictions
```
- Restart Postfix
```
systemctl restart postfix
```
## Result 
IP :**113.160.0.14** 
- dinhha.online - Block
![image](https://github.com/trangnth/BizflyCloudEmail/assets/119484840/bcb5d491-db18-4a31-8bb7-a04542ea386f)

- hadt.space - Not Block
![image](https://github.com/trangnth/BizflyCloudEmail/assets/119484840/a4ade468-c9c0-4a95-a396-3213ec2f13b4)


## References
* [Postfix: Restricting smtpd client access by domain](https://thelowedown.wordpress.com/2008/02/24/postfix-restricting-smtpd-client-access-by-domain/)
* [Kyle D. Dent - Postfix_ The Definitive Guide (2003, O'Reilly Media - p.275)](https://drive.google.com/file/d/1qgfeHMxqyThf6BI5T0blsc37QJV8BGzv/view?usp=sharing)
