```shell
10.75.13.23
root/Qwer1234.com##23
10.75.13.23/phpldapadmin
cn=admin,dc=cpu-os,dc=com
qazWSX2!@4#Edc

10.75.13.16
root/Qwer1234.com##16
10.75.13.16/phpldapadmin
cn=admin,dc=cpu-os,dc=com
qazWSX2!@4#Edc


ldap主从, 
用户登录: 
1. ip.mac/server
2. user/组




dn: olcDatabase={2}bdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=admin,dc=qiban,dc=com
-
replace: olcSuffix
olcSuffix: dc=qiban,dc=com

https://www.sohu.com/a/284300312_283613

ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f a.ldif


ldapsearch -LLL -Y EXTERNAL -H ldapi:/// -b  cn=config olcRootDN=cn=Manager,dc=ldap,dc=com dn olcRootDN olcRootPW
```

