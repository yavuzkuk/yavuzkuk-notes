# Zphisher

Zphisher diğer hacking toollarının aksine adından anlaşılabilceği gibi phishing saldırılar için kullanılır. Kurulumu ve kullanışı oldukça basit olduğu için ve içerisinde çok fazla içerik bulundurduğu için diğer phishing toollarından daha ön plana çıkıyor.

<figure><img src="../assets/zphisher/zphisher.png" alt=""><figcaption></figcaption></figure>

Açık kaynak kodlu olan Zphisher, 30 dan fazla güncel web sitesi arayüzüyle kullanıcıların phishing saldırıları yapmasına olanak tanıyor. Basit olarak sistem şöyle çalışıyor gönderilen phishing web sitesine tıklandığında kullanıcının Ip adresi saldırganın ekranına düşüyor, sonrasında site içinde bulunan input alanlarına kullanıcıların bilgilerini girmesiyle birlikte, saldırganın ekranına girilen bilgileri düşüyor.

Bu işlemlerden sonra daha fazla dikkat çekmemek amacıyla kullanıcı orjinal web sitesine yönlendiriliyor.

Şu ana kadar bu yazımızda basit olarak Zphisher toolunun basit olarak nasıl çalıştığında bahsettik bundan sonra, nasıl kurulacağından ve kullanacağımızdan bahsedeceğiz.

Öncelikle bu kurulum işlemleri için Ubuntu işletim sistemini kullanıyorum. Siz istediğinizi kullanabilirsiniz.&#x20;

Github: [https://github.com/htr-tech/zphisher](https://github.com/htr-tech/zphisher)

Yukarıdaki Github adresini ziyaret ederek kaynak kodları görebilirsiniz.

## Kurulum

```
git clone https://github.com/htr-tech/zphisher.git
```

Komut satırından yukarıdaki komutu yazarak Zphisher dosyasını indiriyoruz.

İndirme işlemleri tamamlandıktan sonra sırasıyla şu komutları yazıyoruz.

```
cd zphisher/
ls
```
Dosyamızın içine girdikten sonra phishing işlemlerimizi yapabilmek için öncelikle toolu çalıştırmamız lazım, bunun için şu komutu yazıyoruz.

```
bash zphisher.sh
```


Komutu yazdıktan sonra karşımıza kısa süreli yükleme ekranı çıkıyor, bu kısa süreli yükleme ekranı toolu her çalıştırdığımızda karşımıza çıkıcak. Yükleme ekranında sonra karşımıza phishing saldırısı yapabileceğimiz web sitelerinin isimleri çıkıyor.

<figure><img src="../assets/zphisher/image (2).png" alt="" width="563"><figcaption></figcaption></figure>

Bu kısımdan hangi web sitesinin phishing için kullanacağımızı seçiyoruz. Ben örnek olması amacıyla Instagramı seçiyorum.&#x20;

<figure><img src="../assets/zphisher/image (3).png" alt=""><figcaption></figcaption></figure>

Bazı web sitelerinin birden fazla seçenekleri olabiliyor. Instagramda bunlardan biri ve bize hangi sayfayı istediğimiz soruyor. Ben burada 01 numaralı siteyi istiyorum.


Sonrasımda karşımıza çıkan ekrandan 02 nolu seçeneği seçiyoruz.&#x20;

Bize kişiselleştirilmiş bir port isteyip istemediğimizi soruyor, bu denememiz için istemiyorum ve  "n" diyoruz.

<figure><img src="../assets/zphisher/image (6).png" alt="" width="458"><figcaption></figcaption></figure>

Sitemiz ayağa kaldırılıyor.

<figure><img src="../assets/zphisher/image (9).png" alt=""><figcaption></figcaption></figure>

Sitemiz yukarıda gördüğünüz gibi çalışır durumda. Açık bir şekilde söylemek gerekirse toolu her çalıştırdığınızda bu ekrana kadar gelemeyebilirsiniz. Bazı denemelerim sonucunda bütün seçimleri yaptıktan sonra yukarıdaki ekranda "Başlatma başarısız oldu, tekrar deneyizi" benzeri ifadeler gördüm. Buna benzer ifadeler görürseniz tekrardan deneyin, yaptığınız ayarları değiştirin.

Sonrasında yukarı ki fotoğrafta gördüğümüz yeşil ile gördüğümüz urli Windows cihazımda tarayıcıda açıyorum.

Tarayıcımızdan giriş yaptıktan sonra karşımıza şu şekil bir sayfa çıkıyor.

<figure><img src="../assets/zphisher/image (10).png" alt="" width="563"><figcaption></figcaption></figure>

Saldırıyı hazırladığımı termina ekranına bakarsak bizim Ip adresimizi görüyor olacağız.

<figure><img src="../assets/zphisher/image (11).png" alt="" width="563"><figcaption></figcaption></figure>

Sonrasında madur kullanıcı olarak giriş bilgilerimizi yazıyoruz.&#x20;

<figure><img src="../assets/zphisher/image (13).png" alt="" width="563"><figcaption></figcaption></figure>

Login butonuna bastıktan sonra girdiğimiz  bilgilerin saldırgan ekranına düşmesi gerekiyor.&#x20;

<figure><img src="../assets/zphisher/image (14).png" alt="" width="563"><figcaption></figcaption></figure>

Evet girdiğimiz bilgiler ekranımıza düştü. Ctrl + C yaptığımızda sitemiz kapanıcak ve daha fazla bilgi toplayamayacağız. Daha önce phishing linkine tıklamış ve bilgilerini giren kişileri görmek için şu komutları kullanabiliriz.

<figure><img src="../assets/zphisher/image (15).png" alt=""><figcaption></figcaption></figure>

Bu komutlar sonucunda karşımıza iki tane dosya çıkıyor. ip.txt dosyasında sitelere giriş yapanların Ip adresleri ve User-agent bilgileri bulunmakta.

usernamas.dat dosyasında ise girilen kullanı bilgileri bulunmakta. Bu dosyaları "cat" komutuyla okuyabiliriz.


> Bu yazı [*Yavuz Kuk*](https://www.linkedin.com/in/yavuzkuk/) tarafından hazırlanmıştır.