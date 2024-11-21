# John the Ripper

<figure><img src="../.gitbook/assets/john-the-ripper.png" alt=""><figcaption></figcaption></figure>

John the Ripper aracı, piyasadaki en iyi bilinen ve çok yönlü hash kırma araçlarından biridir. Aracın nasıl kullanıldığına bakmadan önce hash nedir onu öğrenelim.&#x20;

Hash, herhangi bir uzunluktaki bir veri parçasını alıp onu sabit uzunlukta başka bir biçimde göstermenin bir yoludur. Bu, verinin orijinal değerini maskeler. Bu, orijinal veriyi bir karma algoritmasından geçirerek yapılır. MD4, MD5, SHA1 ve NTLM gibi birçok popüler karma algoritması vardır.&#x20;

Örnek vermek amacıyla [https://www.md5hashgenerator.com/](https://www.md5hashgenerator.com/) sitesinden denemeler yapacağım. "deneme" kelimesinin MD5 hash karşılığı _8f10d078b2799206cfe914b32cc6a5e9_ 32 karakterli bir çıktıdır. Aynı şekilde "a" karakterinin MD5 hash karşılığı ise _0cc175b9c0f1b6a831c399e269772661_ 32 karakterli bir çıktıdır.&#x20;

Hash işlevleri tek yönlü işlevler olarak tasarlanmıştır. Başka bir deyişle, verilen bir girdinin hash değerini elde etmek kolaydır; ancak hash değeri verildiğinde orijinal değeri bulmak zor bir sorundur. Hash değerini metine döndürmek için çeşitli yöntemler kullanılır ama temel olarak hash algoritmaları geri döndürülemezler.

***

Algoritmanın kendisi  geri döndürülemez olsa da. Bu, hash değerlerinin kırmanın imkansız olduğu anlamına gelmez. Örneğin, bir parolanın hash algoritmasını biliyorsanız ve elinizde sık kullanılan şifreleri içeren bir wordlist olduğunu düşünün. Listede bulunan bütün şifreleri verilen algoritmaya göre hashleyip elimizde bulunan hash değeri ile kontrol ederiz. Eşleşiyorlarsa, artık o hashin hangi kelimeye karşılık geldiğini öğrenmiş oluyoruz.

Bu işleme sözlük saldırısı denir ve John the Ripper veya yaygın olarak kısaltıldığı adıyla John, çok sayıda farklı hash türünde hızlı kaba kuvvet saldırıları gerçekleştirmenizi sağlayan bir araçtır.



John the Ripper aracı kendi içinde hash algoritmasını tespit etme özelliğine sahiptir. Bazı durumlarda John the Ripper aracı hash  algortimasını tespit edemeyebilir. Bu durumda [Hash Identifier](https://hashes.com/en/tools/hash\_identifier) denilen siteler kullanılır.&#x20;

Temel olarak John the Ripper aracının 2 farklı kullanım yöntemi vardır. İlk yöntem olarak aracın hash algoritması otomatik olarak tespit etmeye çalıştığı durumdur. Diğer yöntem ise Hashes gibi web sitelerinden hash algoritmasını öğrenip, aracımıza hash alogirtması hakkında bilgi verebiliriz.&#x20;

Elimizde şöyle bir hash bulunuyor: `2e728dd31fb5949bc39cac5a9f066498`

```
john --wordlist=rockyou.txt hash1.txt
```

Bu komutu yazdıktan sonra aracımız bize algoritma tipi hakkında bilgi verir.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Aracımız bize hash algoritması hakkında bilgi verdi ve bu bilgiler eşliğinde işlemlerimizi yapabiliriz. Kullanılan hash tipini format parametresiyle belirtmemiz gerekiyor, hash algoritmalarının bazı özel isimleri olabiliyor bu formatları listelemek için şu komutu kullanabiliriz.&#x20;

`john --list=formats`

Aracımız hash algortiması olarak md5 olarak bize sonuç verdi, format olarak belirtmek için formatlar arasından seçmemiz gerekiyor.

`john --list=formats | grep MD5`

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

MD5 için format belirtirken Raw-MD5 şeklinde belirtmemiz gerekiyor. Aracı kullanırken ayrıca bir wordlist vermemiz gerekiyor. Bunun içinse --wordlist parametresini vermemiz gerekiyor. Wordlist olarak rockyou.txt dosyasını kullanacağım.&#x20;

`john --wordlist=rockyou.txt --format=Raw-MD5 hash1.txt`

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

İlk hash değerimizin asıl orjinal değeri biscuit olduğunu anlamış olduk.

***

İkinci bir örnek olaraksa elimizde şöyle bir hash değeri var. `1A732667F3917C0F4AA98BB13011B9090C6F8065` örnek olması açısından bu hash değerinin kullandığı algoritma yöntemini [Hashes](https://hashes.com/en/tools/hash\_identifier) sitesinden tespit edeceğim.&#x20;

Web sitesi hash algoritması olarak SHA1 olarak sonuç verdi.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

John the Ripper aracının SHA1 için kullandığı parametre şekli nedir öncelikle ona bakalım.

`john --list=formats | grep SHA1`

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Format olarak Raw-SHA1 olarak belirteceğiz. Benzer şekilde komudumuzu yazdıktan sonra hashin değerini ulaşabiliyoruz.

`john --wordlist=rockyou.txt --format=Raw-SHA1 hash2.txt`

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

Üçüncü örneğimizde aynı şekilde bir hash değerini çözeceğiz. Öncelikle hash algoritmasını tespit etmek için Hashes sitesinden algoritma tespiti yapıyoruz.

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

Bulunan hash formatına göre kodumuzu düzenleyip hashin sonucuna ulaşıyoruz.

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

NT hash (veya NTLM), modern Windows işletim sistemlerinde kullanıcı ve servis parolalarını saklamak için kullanılan bir hash formatıdır. Bu, "NT/LM" olarak da adlandırılır; burada "LM", eski Windows sürümlerinde kullanılan bir parola hash formatına atıfta bulunur.

Elimizde `5460C85BD858A11475115D2DD3A82333`Windows tipinde bir hash değerimiz var. Öncelikle hash algoritmasını öğrenmemiz lazım.&#x20;

[Hashes ](https://hashes.com/en/tools/hash\_identifier)sitesinden baktığımızda da şöyle bir sonuç çıkıyor.

<figure><img src="../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

NTLM algoritmasının format tipini öğrenmek için şu komutu kullanıyoruz:

`john --list=formats | grep  NT`

<figure><img src="../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

`john --wordlist=rockyou.txt --format=nt ntlm.txt`

<figure><img src="../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

Nasıl Windows sistemlerin şifrelerini öğrendiysek Linux sistemlerin şifrelerini de öğrenebiliriz bunun için iki farklı dosyaya ihtiyacımız var /etc/shadow ve /etc/passwd dosyaları. Bu iki dosya da kullanıcılar hakkında bilgi tutar. Bu iki dosyada kullanıcılar hakkında bilgiler olduğu için `unshadow` komutuyla hem `/etc/passwd` hem de `/etc/shadow` dosyalarındaki bilgileri birleştirerek işlem yapabilmemizi sağlar.

Öncelikle bu iki dosyayı unshadow komutuyla birleştirmemiz gerekiyor.&#x20;

`unshadow /etc/shadow /etc/passwd > unshadow.txt`

<figure><img src="../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ortaya çıkan dosya üzerinde algoritma tespiti yaptıktan sonra format parametresini gönderip hash değerimizi kırıyoruz.

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

***

John the Ripper'da bulunan "single crack mode", genellikle en hızlı ve basit parola kırma yöntemlerinden biridir. Bu mod, kullanıcının adını ve diğer mevcut bilgileri kullanarak basit parolaları kırmayı amaçlar. Bu yöntem, özellikle zayıf ve tahmin edilebilir parolalar için çok etkilidir.

<figure><img src="../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

Elimde joker kullanıcısına ait böyle bir hash var. Format olarak kullanıcı bilgisi:hash şeklindedir.

Çözüm yöntemi için şöyle bir yöntem izlenir. Kişi bilgilerinde bulunan karakterler farklı şekillerde yazılır. Örneğin:

`Joker , JoKer , j0ker`&#x20;

Öncelikle hangi hash algoritmasını kullandığını öğrenmek için `john --single hash.txt` kodunu yazıyoruz.&#x20;

<figure><img src="../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

Md5 algoritması kullanıyor. Format değeri belirtirken Raw-MD5 şeklinde belirteceğimizi daha önce görmüştük.

`john --single --format=Raw-MD5 hash.txt`

<figure><img src="../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Orjinel değere ulaşıyoruz.

***

John aracıyla Zip dosyalarının şifrelerini de kırabiliriz. Öncelikle zip dosyası Jonh aracının anlayabileceği formata dönüştürmemiz lazım. Bunun için zip2john aracını kullanacağız.

zip2john \<zipDosya> > \<txtDosya>

<figure><img src="../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Herhangi bir format bildirmeden wordlist değerimizi vererek şifreyi kırabiliyoruz.

<figure><img src="../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

Aynı şekilde RAR dosyalarını da John the Ripper ile kırabiliriz. Jonh aracının içeriği anlayabilmesi için bu sefer `rar2john` aracını kullanacağız çıktı üzerinde aynı işlemleri yapıp kıracağız.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Örnek olması amacıyla bir tane de SSH hash değerini elde edeceğiz. Öncelikle elimizde `id_rsa_1605800988509.id_rsa`  adında bir dosya var. Bu dosyayı öncelikle John aracının anlayacağı şekle getirmemiz gerekiyor. Sonrasında wordlist ve dosyayı vererek şifreyi çözebiliriz.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>
