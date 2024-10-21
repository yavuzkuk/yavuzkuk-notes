# Authentication Vulnerabilities

Authentication vulnerabilities, yani kimlik doğrulama zafiyetleri, bir sistemde kullanıcıların kimliğini doğrularken oluşabilecek güvenlik açıklarını ifade eder. Kimlik doğrulama, bir kullanıcının kim olduğunu sistemin doğrulama işlemidir ve bu işlem genellikle kullanıcı adı ve şifre kombinasyonu ile yapılır. Ancak, bu süreçte meydana gelen zafiyetler, kötü niyetli kişilerin yetkisiz erişim sağlamasına neden olabilir.&#x20;

Bu yazıda Burp Suite Academy üzerinden çeşitli örnekler çözeceğiz.

Normal şartlar altında güvenli bir sistemde her kullanıcı kendi hesabına erişebiliyor olmalı. Başka bir kişinin kullanıcı şifresini değiştirmek, brute-force kullanarak şifreyi öğrenmek, MFA (multi-factor authentication) adımlarını atlatmak sistemde büyük zafiyetlere yol açar.

Farklı siteler farklı authentication sistemleri kullanır bazıları sadece şifre ile giriş işlemlerini yönetirken bazıları şifre ve 2FA adımlarıyla giriş işlemini yönetir. Örneklerde bahsettiğim çeşitli giriş işlemlerini aşıp yetkisiz erişim elde edeceğiz.

## [Lab: Username enumeration via subtly different responses](https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-subtly-different-responses)

İlk örneğimizde hangi kullanıcıya saldıracağımızı bilmiyoruz önce kullanıcı adını bulmamız lazım. Potansiyel kullanıcı adlarını ve şifreler bize verilmiş. Öncelikle hedef web sitesinde bulunan giriş safyasına rastgele bilgiler girelim ve arkada giden isteği Burp Suite ile yakalayalım. Yakaladığımız isteği Intrudera gönderelim burada brute-force saldırısı ile hangi kullanıcı hesaplarının olduğunu öğreneceğiz.

Araya girmeyi kapatıp paketin gitmesine izin verirsek şöyle bir hata karşımıza çıkıyor "Invalid username or password.". İsteğimize dönen cevapların içinde bu hatanın olup olmadığını kontrol edeceğiz farklı bir cevap içeren istekte bulunan bizim kullanıcı adımız olucaktır. Şimdi adım adım ilerleyelim.

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

Giriş işlemi için arkada giden paket bu şekildedir. Şifre kısmını şimdilik rastgele bir şeyler ile geçiyoruz. Payload kısmında ise bize sağlanan kullanıcı adlarını kullanıyoruz.

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Bize dönen cevap üzerinde yukarıda belirtiğim hatanın olup olmadığını kontrol etmek için Settings kısmında küçük bir ayar yapmamız lazım. Settings kısmında Grep - Match kısmına yukarıdaki hatamızı eklememiz lazım.

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Bu işlemlerden sonra saldrıyı başlatabiliriz. Yaptığımız filtrelemeyi içermeyen bir istek olucak ve bizim kullanıcı adımızı oradan bulacağız.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

İşlem sonucunda dönen cevap üzerinde aradığımız kelime bir tane hariç bütün cevaplarda bulunuyor. Buradan çıkarmamız gereken bir sonuç var ki kullanıcı adı "mysql" dir. Şimdi ise mysql kullanıcı adını kullanarak şifre üzerinde bir brute-force saldırısı yapabiliriz.

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

mysql kullanıcı adıyla sorunun başında bize verilen potansiyel şifrelerle brute force atağı deniyoruz. Brute-force sonrasında şöyle bir sonuç karşımıza çıkıyor.&#x20;

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Yapılan denemeler sonucunda 302 status code ile dönen bir cevap var bu demek oluyor ki girilen bilgilerden sonra sistem kullanıcıyı yönlendirmiş yönlendirdiği yer kullanıcının profili olabilir admin paneli olabilir. Bundan sonra mysql ve sunshine bilgileri ile giriş yapıp profile sayfasına girerek soruyu çözebiliriz.

Bu tür brute-force açıklarını engellemek için çok fazla deneme yapıldıktan sonra IP banı ya da kullanıcıyı kısa bir süre bekletmek çözüm olarak kullanılabilir.&#x20;

## Lab: Broken brute-force protection, IP block

Bu örneğimizde çok fazla yanlış giriş işleminde bulununca sistem bizi kısa bir süreliğine engelliyor. Sorunun başında `wiener:peter` bilgileriyle sisteme giriş yapabileceğimizden bahsediyor ayrıca saldırıda bulunacağımız kullanıcı adının carlos olduğunu söylüyor.

Sistemin nasıl çalıştığını anlamak için wiener kullanısının şifresini yanlış giriyorum ve 3 yanlış girişten sonra 1 dakika bekleme süremiz oluyor. Bu engelleme işlemi basit seviyede şöyle gerçekleşiyor.

* Arka tarafta bir sayaç var bu sayaç yanlış girilen deneme sayılarını tutuyor eğer belirli bir sayıya ulaşılırsa sistem kısa çaplı bir engel uyguluyor.&#x20;
* Diğer bir yöntem ise brute force atağı yapmaya çalışılan hesabın yanlış deneme sayısını arttırabiliriz.

Bu örneğimizde arka tarafta bir sayaç var ve bu sayaç her doğru girişten sonra sıfırlanıyor. Örnek üzerinden anlatmak gerekirse 2 yanlış girişten sonra sayaç 2 oluyor, üçüncü bir yanlış denemede sistem bize kısa süreli bir engel uygulayacak. Sayacı sıfırlamak için kendi hesabımıza giriş yapmamız gerekiyor sonrasında diğer denemeleri yapabiliriz.&#x20;

Brute force işlemi için giriş işlemlerini Intrudera atmamız lazım. Öncelikle arada giden isteği durduruyoruz ve Intrudera gönderiyoruz.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Kullanıcı adına ve şifre kısmına brute force yapacağımız için o kısımları işaretliyoruz. Payload kısımları için şu şekilde bir yöntem izleyeceğiz. Bize verilen potansiyel şifre listesinde 2 şifre arasına peter ekleyeceğiz. Ayrıca kullanıcı adı içinde bir liste oluşturacağız ve burada da 2 carlostan sonra 1 peter olucak. Bu sayede 2 brute force denemesi sonucunda bir defa sisteme giriş yapıp sayacı sıfırlamış olacağız.

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>Listenin bu şekilde olması gerekiyor.</p></figcaption></figure>

Payload listesini tek tek oluşturmaktansa yapay zeka ile bu listeyi düzenlemesini isteyebiliriz.&#x20;

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption><p>Kullanıcı adı listesi bu şekildedir.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p>Şifre listesi bu şekildedir.</p></figcaption></figure>

Ayrıca isteklerin tek tek ve sırasıyla gönderilmesi için Resource pool sekmesinde bir ayar yapmamız gerekiyor.&#x20;

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Bu ayardan sonra saldırıyı başlatabiliriz.  Saldırını sırasına baktığımızda sırasıyla 2 tane başarısız giriş denemesi ve sonrasında `wiener:peter` bilgileriyle başarılı bir giriş sağlanıyor ve sayaç sıfırlanmış oluyor. Status code değerlerine göre filtrelediğimizde carlos kullanıcı adıyla 302 kodunu alan bir giriş denemesi karşımıza çıkıyor. &#x20;

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Bu sonuç gösteriyor ki `carlos:hockey` bilgileriyle sisteme giriş yapabiliriz ve soruyu çözebiliriz.
