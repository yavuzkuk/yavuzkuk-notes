---
cover: >-
  ../.gitbook/assets/Default_A_futuristic_hightech_illustration_depicts_the_concept_2.jpg
coverY: 69
layout:
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# LFI (Local File Inclusion)

Local File Inclusion kısa haliyle LFI. Arama motorumuza bir web sitesinin adresini girdiğimiz zaman o web sitesinin dosyalarını bulunduran sunucuya istek atmış oluyoruz. Kendisinden ilgili dosyaları bize göndermesini istiyoruz.&#x20;

Normal bir web sitesinde normal bir kullanıcının görmesi gereken dosyalar olduğu gibi görmemesi gereken dosyalar da mevcuttur. İşte LFI (Local File Inclusion) zafiyeti ile, bir saldırgan, web uygulamasının çalıştığı sunucudaki yerel dosyaları okumasına, çalıştırmasına veya dahil etmesine olanak tanıyan bir güvenlik zafiyetidir. Bu zafiyetin ortaya çıkma şekli kullanıcıdan alınan inputun kontrol edilmeden ya da kontrollerin atlatılmasıyla inputun işleme sokulmasıyla ortaya çıkar.

Bazı durumlarda web uygulamaları ekranda gösterecek olacağı sayfaları, fotoğrafları, videoları vb. parametre olarak alır.

<figure><img src="../.gitbook/assets/httpsdomain.png" alt=""><figcaption></figcaption></figure>

Web sitesini geliştiren kişi ?file parametresiyle sunucuda bulunan dosyaları ekrana yansıtabiliyor. Ama aynı şekilde kötü niyetli insanlar bu parametreyi manipüle ederek normal kullanıcıların görmemesi gereken dosya içeriklerini görebiliyor.

Teorik anlamda LFI bu şekilde kod tarafına baktığımızda basitçe şöyle çalışıyor.

<pre><code>&#x3C;?php
<strong>    $file = $_GET['file'];
</strong>    // http://sonmodeltel.com/siparisler.php?file=fatura1.pdf
    
    include($file);
?>
</code></pre>

Yukarıda ki kodun açıklaması şu şekildedir. URL'de bulunan **file** parametresi alınıp **include()** fonksiyonu ile sayfa içine dahil ediliyor. Gördüğünüz gibi herhangi bir kontrol durumu gerçekleşmiyor.

***

LFI hakkında temel bilgileri edindik şimdi normal kullanıcının görmemesi gereken bazı hassas bilgileri içeren dizinlerden bahsedelim.

* **/etc/passwd :** Linux/Unix sistemlerinde kullanıcı bilgilerini içerir. Her kullanıcı için bir satır bulunur ve kullanıcı adı, kullanıcı ID'si, grup ID'si, yorum alanı, ana dizin ve kullanıcı kabuğu gibi bilgileri içerir.
* **/etc/shadow :** Kullanıcı parolalarının hash'lerini içerir. Bu dosya sadece root veya yetkili kullanıcılar tarafından okunabilir.
* **/var/log/ :** Çeşitli sistem ve uygulama log dosyalarını içerir. Bu log dosyaları sistemin ve uygulamaların çalışma durumunu, hataları ve kullanıcı aktivitelerini içerir.

LFI sayesinde parametre üzerinde işlem yaparak hassas bilgi içeren bu dosyalara ulaşabiliriz. Daha da ileriye gitmek istersek LFI ile RCE (Remote Code Execution) yapılabilir. Adım adım, labları çözerek ilerleyeceğiz.

Bu konuyu uygulamalı anlatmak için [Try Hack Me File Inclusion](https://tryhackme.com/r/room/fileinc) labını kullanacağım.

***

<figure><img src="../.gitbook/assets/THMFİLE.png" alt=""><figcaption></figcaption></figure>

Fotoğrafta gördüğünüz gibi normal şartlarda url de bulunan **?file** parametresiyle /var/www/app/CVs/userCv.pdf dosyasına ulaşmamız gerekirken, **?file** parametresini manipüle ederek /etc/passwd dosyasına ulaşmış oluyoruz.

Fotoğrafta görünen ?file parametresi dikkatinizi çekmiştir, _**../../../../../../../../etc/passwd .**_ Parametre değerimizde bulunan **../** bir önceki dizine çıkmak anlamına gelir. Bir sürü kullanmamızın sebebi ise ne kadar kullanırsak kullanalım en fazla / (kök) dizine gidebiliriz.

&#x20;

<figure><img src="../.gitbook/assets/currentPath.png" alt="" width="536"><figcaption></figcaption></figure>

### Lab 1

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

İlk labımızda bir input alanına girdiğimiz dosya isimlerini bize getiren bir mekanizma var. Olmayan bir dosya adını girdiğimizde ise bir hata alıyoruz.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

İlk labımız olduğu için herhangi bir kısıtlama olmadığını düşünüp ve çıkan hataya baktığımızda input alanına yazdığımız içeriğin direkt bir şekilde include (dahil edildiğini) görünce deneme amaçlı input alanına yazıyorum.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Evet herhangi bir kontrol yapılmamış ve direkt bi
