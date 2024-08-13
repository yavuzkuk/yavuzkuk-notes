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

Yukarıda yazdığım kod ile 192.168.1.1 - 192.168.1.255 arasındaki aktif cihazları bize döndürür.
