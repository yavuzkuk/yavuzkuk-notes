# API Testing

Bu yazıda API güvenliğini ele alacağız. API güvenliği kısmına geçmeden önce API'nin ne olduğuna bakalım.

Öncelikle API(Application Programming Interface) Türkçe olarak Uygulama Programlama Arayüzü olarak geçer. Basit olarak iki yazılım arasında çeşitli kurallar ve protokoller kontrolünde birbiriyle iletişime geçmesi diye tanımlanabilir.

API'ler genellikle bir veritabanı ile iletişim kurar ve kullanıcıya belirli veriler sağlar. Bu yapı içinde bulunan router denilen kısımlara HTTP istekleri göndererek API oluşturulurken tanımlanan veriler bize döndürülür.

Örnek bir API ve cevabı şu şekilde olabilir:

```python
GET /users HTTP 1.1
Host: example.com
# HTTP isteği
# /users routerı bize kullanıcıları döndürür


# HTTP response
[
  {
    "name": "Yavuz Kuk",
    "email": "yavuz-kuk@hotmail.com"
  },
  {
    "name": "Gülde Sağlık",
    "email": "gulde-saglık@hotmail.com"
  },
  {
    "name": "Faruk Terzi",
    "email": "farukterzi@gmail.com"
  },
  {
    "name": "Reyyan Saydırman",
    "email": "reyyan-saydirman@gmail.com"
  },
  {
    "name": "Selvi Dere",
    "email": "selvidere@hotmail.com"
  }
]

```

**API Kulanımının Avantajları Nelerdir?**

* İstenilen platform üzerinden istek atılarak verilere ulaşılabilme kolaylığı
* İş yükünün azalması
* Verimlilik

{% embed url="https://www.youtube.com/watch?v=pER95RnijWU" %}

### API Recon

API saldırılarımızı yapmadan önce sistemde kullanılan endpointleri bulmamız gerekiyor. Kullanılan endpointleri bulmak bizim saldırı alanımız genişletir ve daha iyi sonuçlar alabiliriz.&#x20;

API'lerin nasıl kullanılacağını içeren çeşitli dokümanlar bulunur. Eğer herkese açık bir API kullanıyorsanız bu dokümana kolay bir şekilde ulaşabilirsiniz. Bu doküman içinde hangi endpointlerin olduğu ve nasıl kullanıldığı gibi bilgiler yer alır.

Genelde iki tür doküman bulunur insanları okuyup anlayabileceği ve makinelerin anlayabileceği doküman tipleri vardır. Herkese açık olmayan bir API dokümanını bulmak için çeşitli dizinlere istek göndermek denenebilir. API dokümanı bulunduktan sonra sistem kullanılan routerlar üzerinde çeşitli işlemler gerçekleştirebiliriz.

```
/api
/swagger/index.html
/openapi.json
```

## [Lab: Exploiting an API endpoint using documentation](https://portswigger.net/web-security/api-testing/lab-exploiting-api-endpoint-using-documentation)

Bu örneğimizde API dokümanını bularak carlos kullanıcısını silmemiz bizden isteniyor. Giriş bilgileri olarak wiener:peter bilgileri bize verilmiş durumda.&#x20;

Az önce de belirttiğim gibi herkese açık olmayan API dokümanları ulaşılabilir olmamalı. Bu dokümanlar sistemde kullanılan endpointlerin keşfedilmesi için büyük bir kaynaktır.

Bu yüzden endpointleri keşfetmek için API ile ilgili olan dizinleri bulmaya deneyelim. Var olan url adresinin sonuna `/api` eklediğimde dokümana ulaşıyorum.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

3 tane farklı endpoint gözüküyor. Bizden istenen şey carlos kullanıcısını silmek olduğu için 2. endpoint bizim işimize yarayacaktır. Silme işlemini yapmadan önce 1. isteğe bakalım, bir GET isteği göndererek belirli bir kullanıcı hakkında bilgi edinebiliyoruz.&#x20;

```
https://LAB-ID.web-security-academy.net/api/user/carlos
https://0ae200bb04be50a4801e9ef6008c004a.web-security-academy.net/api/user/carlos
```

Tarayıcımızda yukarıdaki linke gittiğimizde carlos kullanıcısının detaylarına ulaşmayı bekliyoruz ama karşımıza `Unauthorized` uyarısı çıkıyor. Giriş yapmadan böyle bir işlem yapamadığımızı anlıyoruz. Bize verilen wiener:peter giriş bilgileriyle sisteme giriş yapalım ve tekrardan bu işlemi deneyelim.

&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Anladık ki API ile işlem yapmak için sisteme giriş yapmış olmamız lazım. Giriş yaptıktan sonra DELETE metodu ile birlikte `/api/user/carlos` router değerine istek göndermemiz gerekiyor. API ortamlarını test etmek için Postman vb. yazılımlar kullanılır. Bu konu için daha basit olması için Burp Suite ile araya girip gönderilen istek üzerinde çeşitli düzeltmeler yapıp isteğimiz istek haline getireceğiz.

Öncelikle Intercept'i aktif hale getirip gönderilen isteği yakalamamız gerekiyor.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Adres çubuğuna [https://LAB-ID.web-security-academy.net/api/user/carlos](https://0ae200bb04be50a4801e9ef6008c004a.web-security-academy.net/api/user/carlos) adresini yazıp entera bastığımızde üstteki istek gönderiliyor. İşaretli olarak gözüktüğü üzere bu bir GET isteği. Bu istek bize carlos kullanıcısı hakkında bilgi getirecektir. Bu metodu DELETE ile değiştirerek isteği gönderiyoruz ve DETELE metoduna sahip router ile iletişime geçiyoruz ve carlos kullanıcısını silmiş oluyoruz.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

***

Bir API endpointi bulduktan sonra çeşitli HTTP metodları kullanarak endpointin vereceği cevabı gözden geçirebiliriz. Çeşitli girdiler vererek cevaplar gözlenir, `Content-Type` değeri manipüle ederek gözlem yapılır.

## [Lab: Finding and exploiting an unused API endpoint](https://portswigger.net/web-security/api-testing/lab-exploiting-unused-api-endpoint)

Bu örneğimizde bizden istenen şey kullanılan API'yi manipüle ederek paramızın yetmediği bir ürünü almamız bizden isteniyor. Giriş bilgileri olarak wiener:peter bilgileriyle giriş yapabiliriz. Hesabımıza girdiğimizde paramızın olmadığını görüyoruz.&#x20;

Sistemde kullanılan API ve routerları bulmak için öncelikle diğer örnekte gördüğümüz dokümanın ulaşılabilir olup olmadığını kontrol etmemiz gerekiyor. Var olan urlin sonuna /api eklediğimizde ulaşılabilir bir API dokümanın olmadığını anlıyoruz.

Zaten soruda da bize gizli API endpointlerini bulup işlemi yapmamız istendiği yazıyor. Gizli API endpointi bulmak için bu süreçte Intercept açık bir şekilde işlemlere devam ediyorum. Bizden istenen ürünün detay sayfasına ulaşırken gönderilen paketler arasından dikkatimi çeken bir istek bulunuyor.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Bu istekte bir endpointe GET metodu ile bir istekte bulunulmuş. Ürünler arasından büyük ihtimalle id değeri 1 olan ürünün fiyatı istenilmiş. Bu isteği repeatera gönderip istek üzerinde çeşitli denemeler yapabiliriz.

```
GET /api/products/1/  # hata
GET /api/products/    # hata
GET /api/products/2/price # id değeri 2 olan kullanıcının fiyat değeri
GET /api              # hata
```

Farklı endpointler denedikten sonra faydalı bir şey bulamıyoruz. Bu sefer HTTP metodunu değiştirip çıktıları karşılaştırabiliriz.

```
PATCH /api/products/1/price HTTP/2
# istek

# cevap
HTTP/2 400 Bad Request
Content-Type: application/json; charset=utf-8
X-Frame-Options: SAMEORIGIN
Content-Length: 93

{"type":"ClientError","code":400,"error":"Only 'application/json' Content-Type is supported"}
```

PATCH metodu dışında diğer cevaplar hata döndürüyor. PATH metoduyla bize dönen cevaba baktığımızda `Content-Type` değeri sadece _"application/json"_ değerini kabul ediyor. İsteğimize Content-Type header değerini ve rastgele bir JSON veri ekleyip gönderelim.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

İsteği gönderdikten sonra bize dönen hata da price değerinin var olmadığından hata veriyor. JSON veri içinde göndereceğimiz price değeri bizim id değeri 1 olan ürünümüzün yeni fiyatı olacaktır.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

PATCH metodu ile yeni bir istek gönderdik ve fiyatımız değişmiş olması lazım. Kontrol için metodumuzu GET yapıp body de bulunan verileri siliyoruz ve fiyatı öğreniyoruz.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Fiyatımızı da düşürdükten sonra ürünü sepetimize ekleyip satın alabiliyoruz ve soruyu çözüyoruz.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

***

### Gizli parametrelerin bulunması

Bir endpoint bulduktan sonra zorunlu parametrelerin dışında opsiyonel olan ve kullanıcıya belirtilmeyen parametreler bizim işimize yarayabilir. Örnek vermek gerekirse /api/user/update endpointini buldunuz ve bu endpoint email ve şifre parametrelerini alıyor gözüküyor.&#x20;

Aynı endpoint bize GET isteği çeşitli gizli parametreleri açığa çıkarmak konusunda yardımcı olabilir.&#x20;

```
# PATCH /api/users/123

{
    "username": "yavuzkuk",
    "email": "yavuzkuk@example.com",
}
```

```
# GET /api/users/123

{
    "id": 123,
    "name": "Yavuz Kuk",
    "email": "yavuzkuk@example.com",
    "isAdmin": "false"
}
```

Yukarıdaki GET isteğiyle birlikte bizim bilmediğimiz parametreleri ortaya çıkarmış olduk. Eğer PATH metoduna isAdmin değeri ekleyip çeşitli farklı değerler ile deneme yanılma yöntemiyle parametrenin nasıl etkilediğini bulabiliriz.

