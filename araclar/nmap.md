# Nmap

<figure><img src="../assets/nmap/nmaplogo.jpg" alt=""><figcaption></figcaption></figure>

Nmap(Network Mapper) ağ keşfi ve güvenlik denetimleri için kullanılan açık kaynaklı ve güçlü bir tarama aracıdır. Nmap sayesinde ağda bulunan cihazları, bu cihazların kullandığı servisleri, servislerin versiyonları, açık olan portları, işletim sistemlerini listeleyen bir araçtır. 

Nmap'in bize vermiş olduğu çıktılar sayesinde hedef sistemin kullandığı servisler üzerinden zafiyet taraması yapabiliriz. 

```sudo apt install nmap```

Yukarıdaki komut sayesinde sistemimize Nmap aracını yükleyebiliriz.

Nmap'in normal kullanış şekli şu şekildedir.

```nmap <ip adresi/adresleri>```

Nmap verdiğimiz Ip adresi yada Ip adres aralığında olan cihazların port taramasını yapar. Eğer Nmap aracımıza bir Ip aralığı verirsek örnek vermek gerekirse ```10.10.123.0/24``` Nmap öncelikle bu aralıkta aktif olan cihazları tarar sonrasında sadece aktif olan cihazların port taramasını gerçekleştirir. 

Bazı durumlarda biz sadece aktif cihazları görmek isteriz. Port taraması yapmasını istemeyiz. 

## -sn 

-sn parametresi Nmap aracının port taraması yapmasını engeller ve sadece aracın aktif cihazları göstermesini sağlar.

```nmap -sn 192.168.1.0/24```

Yukarıda yazdığım kod ile 192.168.1.1 - 192.168.1.255 arasındaki aktif cihazları bize döndürür. Port taramasına geçmeden önce hedef sistemin aktif olup olmadığını anlamamız lazım.

Bunun için çeşitli parametreler kullanılır. 

## -PE
Komut satırı sayesinde istediğimiz sisteme ```ping``` atabiliyoruz. Aktif cihazları taramak için bu ping atma yöntemini kullanacağız. 

Birçok güvenlik duvarı ICMP echo engeller; MS Windows'un yeni sürümleri, varsayılan olarak ICMP echo isteklerini engelleyen bir ana bilgisayar güvenlik duvarı ile yapılandırılmıştır. 

```sudo nmap -sn -PE 10.10.68.0/24```

<figure><img src="../assets/nmap/pe.png" alt=""><figcaption></figcaption></figure>

## -PP

ICMP echo istekleri genellikle engellendiğinden, bir sistemin çevrimiçi olup olmadığını anlamak için ICMP Timestamp (Zaman damgası) veya ICMP Address Mask (Adres Maskesi) isteklerini de düşünebilirsiniz. Nmap, zaman damgası isteğini kullanır ve Zaman Damgası yanıtı alıp almayacağını kontrol eder.

-PP parametresiyle birlikte hedefe sistem saatini sorgulamak için paket gönderilir. Eğer sistem aktif ise sistem geri dönüş sağlar.

```sudo nmap -sn -PP 10.10.68.0/24```

<figure><img src="../assets/nmap/pp.png" alt=""><figcaption></figcaption></figure>

## -PM
Benzer şekilde Nmap, adres maskesi sorgularını kullanır ve adres maskesi yanıtı alıp almadığını kontrol eder.

```sudo nmap -sn -PM 10.10.68.0/24```

<figure><img src="../assets/nmap/pm.png" alt=""><figcaption></figcaption></figure>


## -PS

Hedef sisteme SYN paketi gönderilir eğer hedef sistem açıksa SYN-ACK paketi gönderir. Eğer hedef sistem aktif değilse RST paketi gönderir.

<figure><img src="../assets/nmap/ps.png" alt=""><figcaption></figcaption></figure>

## -PA

Hedef sisteme ACK paketi gönderir. Bu tarama işlemini yapabilmek için Nmap'i root yetkilerine sahip olarak çalıştırıyor olmanız gerekir. root yetkilerine sahi olmayan bir kullanıcı olarak denerseniz, Nmap 3 yönlü bir el sıkışma girişiminde bulunacaktır.

```sudo nmap -PA -sn 10.10.68.220/24```

<figure><img src="../assets/nmap/pa.png" alt=""><figcaption></figcaption></figure>


## -PU

Son olarak, ana bilgisayarın çevrimiçi olup olmadığını keşfetmek için UDP kullanabiliriz. TCP SYN ping'in aksine, açık bir porta bir UDP paketi göndermenin herhangi bir yanıta yol açması beklenmez. Ancak, kapalı bir UDP portuna bir UDP paketi gönderirsek, bir ICMP port erişilemez paketi almayı bekleriz; bu, hedef sistemin açık ve kullanılabilir olduğunu gösterir.

Aşağıdaki şekilde açık bir UDP portuna gönderilen ve herhangi bir yanıt tetiklemeyen bir UDP paketini görüyoruz. Ancak herhangi bir kapalı UDP bağlantı noktasına bir UDP paketi göndermek, dolaylı olarak hedefin çevrimiçi olduğunu belirten bir yanıtı tetikleyebilir.

```sudo nmap -PU -sn 10.10.68.220/24```

<figure><img src="../assets/nmap/pu1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../assets/nmap/pu2.png" alt=""><figcaption></figcaption></figure>


## Port nedir?

Port taraması yapmadan önce portun ne olduğundan bahsedelim. Bilgisayarlar ve sunucular üzerindeki portlar, uygulamaların bilgi alışverişi yapabilmesi için kullanılan sanal giriş ve çıkış noktalarıdır. Bir port, belirli bir protokol (örneğin, TCP veya UDP) ve belirli bir uygulama tarafından iletişim için kullanılır. 

0'dan 65535'e kadar numaralandırılmıştır.

Çok bilinen portlar şunlardır:
- Port 80: HTTP (web trafiği)
- Port 443: HTTPS (güvenli web trafiği)
- Port 22: SSH (güvenli uzaktan erişim)
- Port 25: SMTP (e-posta iletimi)


