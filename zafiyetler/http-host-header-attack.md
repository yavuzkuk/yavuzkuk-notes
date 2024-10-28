# HTTP Host Header Attack

Merhabalar bu yazımızda HTTP Host Header saldırısı konusundan bahsedeceğiz.&#x20;

<figure><img src="../.gitbook/assets/_f5e89b05-1efb-4280-a675-11e1c5d7776c.jpeg" alt=""><figcaption></figcaption></figure>



Bir web sitesine istek atarken arka tarafta çeşitli istekler gönderiliyor. Öncelikle örnek bir web sitesi üzerinden bu istekleri inceleyelim:

example.com adlı web sitesinin iletişim sayfasına bir istek gerçekleştiriliyor.&#x20;

```
GET /contact HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: tr-TR,tr;q=0.5
Connection: keep-alive
```

Yukarıdaki isteği parça parça inceleyelim:

* **GET / HTTP/1.1**: Bu satır, HTTP metodunu (`GET`), yolunu (`/`) ve HTTP versiyonunu (`HTTP/1.1`) belirtir.
* **Host**: İstek gönderilen sunucunun adresi; `example.com`.
* **User-Agent**: İsteği gönderen tarayıcı veya uygulama hakkında bilgi.
* **Accept**: Tarayıcının hangi türde içerikleri kabul edeceğini belirtir (HTML, XML, resim vb.).
* **Accept-Language**: İsteğin dil tercihlerini belirler (örneğin, `tr-TR` İngilizce dil seçeneği).
* **Connection**: İstek sonrası bağlantının durumu hakkında bilgi verir (`keep-alive`, bağlantıyı açık tutar).

Bizim burada ilgileneceğimiz ve manipüle edeceğimiz kısım Host header değeridir.&#x20;

### Host header değeri ne işe yarar?

Host header değeri ile birlikte hangi hedef sisteme istek atacağımızı anlamış oluruz. Host headerı HTTP/1.1 ile zorunlu bir başlık olarak kabul edilir. Eğer host değeri silinirse modern web sunucularında gelen istekler 400 Bad Request ile geri döndürülür.&#x20;

Teknolojinin ilk dönemlerinde, tek bir sunucu üzerinde genellikle yalnızca bir web sitesi veya uygulama barındırılıyordu. Bu durumda, Host header değerine IP adresi verildiğinde sunucu hangi siteye yönlendirileceğini kolayca anlayabiliyordu. Ancak günümüzde, sanallaştırma ve bulut teknolojilerinin gelişmesiyle aynı IP adresi üzerinde birçok farklı site veya uygulama barındırılabiliyor.

Modern altyapılarda, bir sunucu veya IP adresi genellikle birçok farklı alan adını (domain) destekler. Bu durumda, Host header'ın alan adıyla birlikte gönderilmesi, sunucunun istek yapılan kaynağı doğru bir şekilde belirleyebilmesi için gereklidir. Örneğin, aynı IP adresine sahip bir sunucu hem `example.com` hem de `example.net` sitelerini barındırıyorsa, Host header değeri olmadan sunucu gelen isteğin hangi siteye ait olduğunu bilemez.

### Host header saldırısı nasıl gerçekleşir?

Host header saldırısının gerçekleşmesi için öncelikle saldırganın gönderilen isteği durdurup bu istek üzerinde çeşitli işlemler yapması gerekir. Çeşitli sistemler programlanırken istek ile birlikte gelen host header değerini alır ve kullanırlar. Gerekli kontroller yapılmazsa ve sistem direkt bir şekilde host header değerini alıp kendi üzerinde kullanırsa çeşitli sıkıntılara yol açabilir.

### Host header zafiyetlerini test etme

Öncelikle hedef sistem üzerinde host header zafiyetini bulmamız gerekiyor. Bu yüzden Burp Suite aracıyla gönderilen paketleri durdurup çeşitli manipülasyonlar yapıp zafiyeti tetiklememiz gerekiyor.&#x20;

* Rastgele host header değeri eklenerek sistemin vereceği cevap gözlenebilir. Eğer sunucu cevap veriyorsa, sitenin bu hatalı domaini varsayılan bir seçenek olarak kabul ediyor olabilir. Bu durumda, uygulamanın Host header'ı nasıl işlediğini daha detaylı inceleyebilirsiniz. Ancak çoğu durumda, geçersiz bir Host header gönderdiğinizde "Invalid Host header" gibi bir hata alabilirsiniz.
* Bazı şartlar altında vereceğimiz domain adını kontrol eder vermiş olduğumuz port adresini önemsemezler. Ayrıca bazı siteler sadece aynı subdomaine sahip olan istekleri işleyebilirler.
* Bazı sistemler farklı şekilde içerikleri değerlendirebilirler. Bazı sistemleri manipüle etmek için ikinci host header değeri eklememiz gerekebilir. Gönderilen istekte toplamda 2 tane host header değeri olur ve buna göre bir zafiyet ortaya çıkar.&#x20;

```
GET /contact HTTP/1.1
Host: example.com
Host: badsite.com
```

* Bazı sistemlerde ise host header değerinin yerine geçebilecek çeşitli header değerleri vardır. Bu header değerlerini vererek host header değerini manipüle etmiş oluruz. Özellikle proxy veya yük dengeleyici sistemlerde, Host header yerine X-Forwarded-Host veya benzeri bir header kullanılabilir. Böyle durumlarda, doğrudan Host header’a müdahale etmek yerine X-Forwarded-Host header’ı üzerinden zararlı veriyi eklemek mümkündür

Host header değeri dışında kullanılabilecek bazı header'lar şunlardır:

* X-Forwarded-Host
* X-Host
* X-Forwarded-Server
* X-HTTP-Host-Override
* Forwarded

### Host header saldırısın zararları nelerdir?

* Password reset poisoning (Şifre değiştirme saldırısı)
* Web cache poisoning (Web önbelleği saldırısı)
* Exploiting classic server-side vulnerabilities (Klasik sunucu taraflı zafiyetlerin istismarı)
* Bypassing authentication (Kimlik doğrulamayı atlatma)
* Virtual host brute-forcing (Sanal ana makine brute-forcing (kaba kuvvet) saldırısı)
* SSRF
* Connection state attacks (Bağlantı durumu saldırıları)

***

Host headerın ne olduğu ne tür sorunlar çıkarabildiğini anladıktan sonra örneklerimize bakalım.

## [Lab: Basic password reset poisoning](https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning/lab-host-header-basic-password-reset-poisoning)

İlk örneğimiz başka bir kullanıcının şifresini yenileyerek hesabını ele geçirmek olacak. Öncelikle normal bir sistemin nasıl çalıştığını düşünelim. Şifre değiştirme işlemleri temelde iki şekilde gerçekleşiyor, eğer şifrenizi biliyorsanız sisteme giriş yapar ve profil kısmından yeni şifrenizi girip şifrenizi değiştirebilirsiniz. Başka bir yöntem olarak da giriş ekranında bulunan şifremi unuttum kısmına mail adresinizi ya da kullanıcı adınızı girerek size gelen linke tıklayarak şifrenizi değiştirebilirsiniz.&#x20;

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

Bir saldırgan olarak tabii ki de hedefimizde olan kişinin şifresini bilmiyoruz. Bunun için hedefimizin kullanıcı adını kullanarak kendi adına bir şifre yenileme bağlantısı oluşturacağız.
