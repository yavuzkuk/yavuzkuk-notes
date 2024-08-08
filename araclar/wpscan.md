# Wpscan

<figure><img src="../.gitbook/assets/wpscan_logo.png" alt=""><figcaption></figcaption></figure>

Wpscan açık kaynak kodlu geliştirilmiş ve terminalde çalıştırılan bir araçtır. WordPress kullanan sitelerde tarama yapmak için kullanılır.

WordPress siteleri hakkında temel bilgileri ve varsa güvenlik zafiyetleri hakkında bilgi verir. Kali Lİnux üzerinde yüklü olarak gelir.&#x20;

Biraz Wpscan aracının yapabildiklerinden bahsedelim:

* Tema taraması
* Eklenti taraması
* Brute Force
* Dizin taraması vb.

Eğer başka bir dağıtım kullanıyorsanız aşşağıdaki kod ile Wpscan aracınızı indirebilirsiniz.

```
gem install wpscan
```

Aracımızın sahip olduğu parametreleri görmek için şu komutu kullanıyoruz:

```
wpscan -h
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Bir web sitesi üzerinde tarama gerçekleştireceğimiz için bir url parametresine ihtiyacımız var. `--url` parametresiyle test edeceğimiz web sitesini parametre olarak veriyoruz.

Ayrıca aracın hangi türde bir tarama yapacağından bahsetmemiz lazım bunun içinde `--enumerate` parametresini kullanıyoruz.

\--enumerate parametresiyle seçenekler vermemiz lazım:

* t : Popüler temaları tarar
* vt : Zafiyetli temaları tarar
* p : Popüler eklentileri tarar
* vp : Zafiyetli eklentileri tarar
* ap : Bütün eklentileri tarar

Verilen --url parametresine göre popüler temaları tarar:

```
wpscan --url <ip/url> --enumerate t
```

\-vt parametresiyle zafiyetli temaları tarar:

```
wpscan --url <ip/url> --enumerate vt
```

\-p parametresiyle popüler eklentileri tarar:

```
wpscan --url <ip/url> --enumerate p
```

\-vp parametresiyle zafiyetli eklentileri tarar:

```
wpscan --url <ip/url> --enumerate vp
```

\-at parametresiyle bütün temalarda arama yapar:

```
wpscan --url <ip/url> --enumerate at
```

\-ap parametresiyle bütün temalarda arama yapar:

```
wpscan --url <ip/url> --enumerate ap
```
