---
cover: >-
  ../.gitbook/assets/Leonardo_Phoenix_A_hauntingly_minimalist_digital_illustration_2.jpg
coverY: 414
---

# JWT Bypass

Bu yazıda JWT Bypass hakkında bilgiler vereceğim. Ama öncesinde JWT nedir onu anlayalım. JWT (JSON Web Token), kimlik doğrulama ve veri güvenliği için yaygın olarak kullanılan bir standarttır. İnternette özellikle API'lar, web servisleri ve mikroservis mimarileri gibi çeşitli yerlerde veri alışverişini güvenli hale getirmek için tercih edilir.

JWT, üç farklı kısımdan oluşur. Bu kısımlra . (nokta) karakteriyle birleştirilir.&#x20;

JWT'nin başlık kısmı header ismini alır ve JWT değerinin meta verilerini (token türü (JWT) ve imzalama algoritması (genellikle HMAC SHA256 gibi) saklar.&#x20;

Payload JWT'nin ikinci kısmıdır ve en önemli kısmıdır, veri taşır. Bu veri genellikle kimlik doğrulama ile ilgili bilgileri içerir (kullanıcı kimliği, yetki seviyeleri gibi).

JWT'nin imza kısmı, token’ın bütünlüğünü garanti altına alır. JWT yapısı bu üç yapıyı içerir. Header ve payload değerleri base64url ile encode edilir. İmza kısmı ise base64url ile encodelanmış header ve payload değerlerinin, gizli bir anahtar ile hashlenmesiyle ortaya çıkar.

Gizli anahtarın sistem haricinde kimse tarafından bilinmemesi gerekir. Eğer gizli anahtar biliniyorsa JWT çok kolay şekilde bypass edilebilir.

Eğer biz gizli anahtarı bilmeden payload üzerinde bir değişiklik yapmaya çalışırsak adım adım şu işlemler gerçekleşir:

* Payload üzerinde değişiklik yapıldığı için JWT'nin ikinci kısmı olan payload kısmı değişir.
* Payload kısmı değişiceği için imza kısmı da değişir ama bu esnada biz gizli anahtarı bilmediğimiz için imza kısmı doğru düzgün şekilde oluşmaz.
* Doğru bir şekilde oluşmayan JWT değerini uygulamaya verdiğimizde uygulama header ve payload değerlerini gizli anahtar ile hasleyerek bizim vermiş olduğumuz JWT değerinin imza kısmı ile karşılaştırır.
* İmzalar farklı ise JWT değerine izinsiz müdahele edilmiştir demektir.

Adım adım JWT Bypass lablarını çözelim.&#x20;

### [Lab 1 - İmza kontrolü yapılmazsa](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)

Bu labda bizden istenen şey JWT üzerinde değişiklik yaparak admin panele erişmek ve carlos kullanıcısını silmek. Öncelikle wiener:peter bilgileriyle sisteme giriş yapıyoruz.

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Geliştirici konsolundan sistem tarafından bize atanan JWT değerini görüyoruz. [JWT-Decoder](https://fusionauth.io/dev-tools/jwt-decoder) sitesinden JWT hakkında bilgi alabiliyoruz. Yanlış yapılandırılan bazı sistemler elde ettiği JWT tokenlarında imza kontrolü yapmıyor.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Girmiş olduğumuz kullanıcı adı payload kısmında sub olarak karşımıza çıkıyor. Bu kısmı `administrator` olarak değiştirip yeni oluşan JWT değerini eski JWT ile değiştiriyoruz. Sayfayı yenilediğimizde admin panele ulaşım carlos kullanıcısını silebiliyoruz.

### [Lab 2 - İmza eklenmezse](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification)

Normal şartlar altında JWT'ler, güvenliği sağlamak için bir imzaya sahip olurlar. İmza, JWT'nin bütünlüğünü ve doğruluğunu garanti eder. Ancak, imzasız JWT'ler de bazı durumlarda kullanılabilir. İmzasız bir JWT, herhangi bir doğrulama veya güvenlik önlemi sağlamaz ve bu durum güvenlik açısından bazı riskler doğurabilir.

Bu işlemler için JWT tokenının imza kısmını silmemiz gerekiyor. Ayrıca JWT header kısmında bulunan alg key değerini none olarak değiştirmemiz lazım. Bu sayede kontrol yapılan sisteme herhangi bir hash algoritması kullanılmadığını belirtmiş oluruz.

Bu labda bu bahsettiğim durum simüle edilmiş. Öncelikle sisteme daha önce verdiğim bilgilerle erişim JWT'yi decode ediyoruz. Önce header da bulunan alg value değerini none yapıyoruz sonrasında payload da bulunan sub değerini administrator olarak değiştiriyoruz.

Son olarak oluşan JWT'den imza kısmını siliyoruz. İmzadan önce gelen . (nokta) karakterini silmiyoruz.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Bu JWT'yi sisteme verdikten sonra başarılı bir şekilde admin paneline ulaşabiliyor ve carlosu silebiliyoruz.

### [Lab 3  - Brute Force](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-weak-signing-key)

Daha önce de belirttiğim üzere imza kısmını oluşturmak için gizli bir anahtar kullanıyoruz. Bu anahtarın kolay şekilde tahmin edilmemesi, brute-force ataklarıyla kırılmaması gerekir. Eğer gizli şifre ele geçirilirse JWT sorunsuzca değiştirilebilir.

Bu labda basit bir JWT gizli şifresini kırarak admin panele ulaşacağız. Öncelikle sisteme giriş yapıyoruz.&#x20;

Bu işlem için elde ettiğimiz JWT değerini jwt.txt isminde bir dosyaya kayıt ediyoruz ve sonrasında sık kullanılan JWT şifrelerini içeren [wordlist](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list) ile brute-force saldırısı yapıyoruz.

Brute force için Hashcat aracını kullanacağım.

```
hashcat -a 0 -m 16500 jwt.txt /usr/share/Wordlists/jwt-secret.txt
```

Bu komut ile gizli anahtarı çözmesi için brute force atağı başlatıyoruz.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

JWT gizli şifresini de öğrendikten sonra kolay bir şekilde JWT üzerinde değişiklik yapabiliriz. [JWT.io](https://jwt.io/) sitesinden gizli anahtarı girip, sub değerini değiştirdikten sonra elde ettiğimiz JWT'yi sisteme ekliyoruz.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Bu işlemlerden sonra admin paan
