# SMB (Server Message Block)

SMB protokolü ağ üzerindeki dosya paylaşımını, yazıcı paylaşımını ve diğer kaynaklara erişimi sağlayan bir ağ dosya paylaşım protokolüdür.&#x20;

SMB, istemci-sunucu modelini kullanarak çalışır. İstemci, dosya paylaşımı veya diğer kaynaklara erişmek için sunucuya bir istekte bulunur. Bu istekler SMB paketleri aracılığıyla iletilir ve sunucu, istemcinin isteğini işler. Bu yapı dosya okuma, yazma, açma, silme gibi işlemleri destekler.

SMB 139,445 portları üzerinde çalışır.

SMB protokolünü daha iyi anlayabilmek için SMB protokolü kullanan bir sistem üzerinden ilerleyeceğiz.

Öncelikle hedef sistemde bulunan açık portları listelememiz gerekiyor.  Nmap ile tarama yapıyoruz ve şöyle bir çıktı alıyoruz.

```
yavuz@Lenovo:~/Downloads$ nmap 10.10.239.13
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-13 03:27 +03
Nmap scan report for 10.10.239.13
Host is up (0.31s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE    SERVICE
22/tcp   open     ssh
139/tcp  open     netbios-ssn
445/tcp  open     microsoft-ds
8007/tcp filtered ajp12
```

Hedef sistemde 139 ve 445 portları açık hedef sistemde SMB çalıştığı anlamına gelir. SMB sistemleri hakkında bilgi toplamak için enum4linux isminde bir araç kullanıyoruz. Bu araca çeşitli parametreler verip hedef sistemde paylaşılan dosyalar, kullanıcılar vb. içerikler hakkında bilgi edinebiliyoruz.

enum4linux aracının help sayfası ile sistemde çeşitli taramalar gerçekleştirebiliriz.

```
yavuz@Lenovo:~/Downloads$ enum4linux -h
Usage: enum4linux [options] ip

Options are (like "enum"):
    -U        get userlist
    -M        get machine list*
    -S        get sharelist
    -P        get password policy information
    -G        get group and member list
    -d        be detailed, applies to -U and -S
    -u user   specify username to use (default "")  
    -p pass   specify password to use (default "")   

The following options from enum.exe aren't implemented: -L, -N, -D, -f

Additional options:
    -a        Do all simple enumeration (-U -S -G -P -r -o -n -i).
              This option is enabled if you don't provide any other options.
    -h        Display this help message and exit
    -r        enumerate users via RID cycling
    -R range  RID ranges to enumerate (default: 500-550,1000-1050, implies -r)
    -K n      Keep searching RIDs until n consective RIDs don't correspond to
              a username.  Impies RID range ends at 999999. Useful 
	      against DCs.
    -l        Get some (limited) info via LDAP 389/TCP (for DCs only)
    -s file   brute force guessing for share names
    -k user   User(s) that exists on remote system (default: administrator,guest,krbtgt,domain admins,root,bin,none)
              Used to get sid with "lookupsid known_username"
    	      Use commas to try several users: "-k admin,user1,user2"
    -o        Get OS information
    -i        Get printer information
    -w wrkg   Specify workgroup manually (usually found automatically)
    -n        Do an nmblookup (similar to nbtstat)
    -v        Verbose.  Shows full commands being run (net, rpcclient, etc.)
    -A        Aggressive. Do write checks on shares etc
```

```
enum4linux -<parametre> 10.10.239.13
```

Hedef sistemde çeşitli bilgi edinebiliriz. Öncelikle paylaşılan dosyaları görmek için şu komutu yazalım.

```
enum4linux -S 10.10.239.13
```

Hedef sistemde paylaşılan dosyaları görmek için kullanılır ve bize çıktı olarak profiles adında bir klasörü paylaştığını belirtiyor.

SMB paylaşılan dosyalara ulaşmak için smbclient adında bir istemciye ihtiyacımız var. smbclient aracıyla birlikte hedef sisteme ve paylaşılan dosyaya bağlanabiliriz.&#x20;

```
yavuz@Lenovo:~/Downloads$ smbclient //10.10.239.13/profiles -U Anonymous
```

Hedef sistemlere bağlanırken Anonymous olarak ve şifre girmeden bağlanmayı denememiz gerekiyor. Gerekli kontroller yapılmazsa basit bir şekilde sisteme giriş yapabiliriz.

Yukarıdaki komuttan sonra sistem bize şifre sorucaktır herhangi bir şey yazmadan entera basıyoruz ve sistemdeki dosyalara erişiyoruz. Sistemde .ssh klasörü altında gizli anahtar bulunmakta ve daha önce gerçekleşen SSH bağlantıları bulunmakta. Daha önce gerçekleşen SSH bağlantılarına baktığımızda cactus adlı bir kullanıcı sisteme bağlanmış.&#x20;

Elde ettiğimiz gizli anahtar ile cactus kullanıcısı olarak sisteme giriş yapıyoruz ve bizden istenen flag değerine ulaşıyoruz.
