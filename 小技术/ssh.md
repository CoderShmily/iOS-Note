# SSH
```
liujialin125deMac-mini:~ xbed$ ssh
ssh          ssh-add      ssh-agent    ssh-keygen   ssh-keyscan  sshd
liujialin125deMac-mini:~ xbed$ ssh-keygen -t rsa -C "zhangh@xbed.com.cn"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/xbed/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/xbed/.ssh/id_rsa.
Your public key has been saved in /Users/xbed/.ssh/id_rsa.pub.
The key fingerprint is:
ad:27:63:3e:3e:0c:c2:d8:1f:c0:e4:bd:42:b9:d5:3c zhangh@xbed.com.cn
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|    .            |
|   + o o         |
|    * o E.       |
|   = + .S..      |
|  . * +  .       |
|     + += .      |
|      .o++       |
|       .oo       |
+-----------------+
liujialin125deMac-mini:~ xbed$ cd .ssh/
liujialin125deMac-mini:.ssh xbed$ cat id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/+zJhLkEJa+Fez9MpV3xxdXxyK81GE7ZO8eb/Usi9DCPkgWUy9kq2JCdzLF1s7oYeD/7TBQUwB0liBe4gjkGo+xYx+SuAE23fp9Yux/oDH+JxeLwJmhuANnlAHkx9mk7TGVu/b8xB17H697HvJHW6/28ywv3dycy/lAGT4X3UDrzlyhTikQhn8QET45CC3LIpHp+O/m63cPPILWDoNk72DKjXQgTWVjq7IghWPXlZouJ0xDKd/0GfHyKqF+GZwycqjhCDJIqeeC6YbjCoawaCBeSPr7Z3+yNCoo2Lz8JZFZ1PsYp5oqyKeaJ09tLvJRRnaAchSMnsE6hhiRDM20ln zhangh@xbed.com.cn
```