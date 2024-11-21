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

### API Recon

API saldırılarımızı yapmadan önce sistemde kullanılan endpointleri bulmamız gerekiyor.&#x20;
