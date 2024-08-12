# Hashcat

<figure>
<image src="../assets/hashcat/hashcatLogo.png">
</figure>

Hashcat açık kaynaklı bir hash çözümleme tooludur. Çoğu web sitesinde şifreler ile sisteme giriş işlemleri kontrol edilir. Bu kontrollerin sağlanması için ise bir veri tabanına ihtiyaç vardır. Güvenli bir web sitesinde kullanıcıların şifreleri düz bir metin olarak değil, hashlenmiş bir şekilde tutulması gerekiyor. 

Hackerlar bu veri tabanını ele geçirdiklerinde şifreleri direkt bir şekilde okuyamayacaktır. Öncelikle şifreleri kırması gerekmektedir. İşte bu yüzden <a href="https://hashcat.net/hashcat/">Hashcat</a> kullanılır.

Hashcat kullanımı oldukça basit bir tool olarak öne çıkar. MacOS, Windows ve Linux desteği sunar. Kali Linuxta yüklü olarak gelir.

Günümüzde çok gelişmiş ekran kartları ve işlemciler geliştirilmiştir. Hashcat hem işlemciyle (CPU) hem de ekran kartıyla (GPU) ile hashleri kırmaya çalışır. Daha hızlı bir sonuç almak istiyorsanız ekran kartları daha iyi bir seçim olacaktır çünkü ekran kartları (GPU), yüksek paralel işlem yetenekleri ile işlemcileri (CPU) geçecektir.

Hashcat'in nasıl çalıştığına geçmeden önce hash ne demek kısaca bundan bahsedelim. Hash bir verinin geri döndürülemez şekilde şifrelenmesine denir. Geri döndürülemez derken şunu anlamamız gerekiyor hashlenmiş veriyi tekrar eski haline getiremiyoruz.

Örnek vermek gerekirse günümüzde en sık karşılaştığımız hash türleri şunlardır:

- SHA-1ㅤ
- SHA-2 
- SHA-3ㅤ
- MD2 
- MD4
- MD5

### Kurulum

Linux üzerinde kurulum için ```sudo apt install hashcat``` komutunu çalıştırmamız gerekiyor.

<figure><image src="../assets/hashcat/install.png"></figure>

Sonrasında toolumuzun doğru bir şekilde çalışıp çalışmadığını öğrenmek için ```hashcat --help``` komutunu yazıyoruz.

<figure><image src="../assets/hashcat/hashcathelp.png"></figure>

Karşımıza uzun bir yardım dökümanı karşımıza çıkıyor.

-----

Hashcat toolumuzu kullanmadan önce alması gereken temel parametrelerden bahsedelim. Öncelikle bizim hangi hash türünden bir dönüştürme yaptığımızı belirtmemiz gerekiyor. 

Peki biz hash tipini nasıl öğreneceğiz bunun için farklı toolar ve online web siteleri var. Ayrıca hashcate verilen bir parametre ile hashlerin tipini tahmin etmesini sağlyabiliyoruz ama ne kadar doğruluk oranı var tam olarak bilemiyorum.

Hash tipi öğrenmek için bazı web siteleri:
- <a href="https://www.tunnelsup.com/hash-analyzer/">Hash Analyzer</a>
- <a href="https://hashes.com/en/tools/hash_identifier">Hash Identifier</a>

Bu sitelere benzer arama yapmak için arama motorunuz üzerinden "Hash Analyzer/Identifier" diye arama yapabilirsiniz.

Ayrıca Hashcat toolumuzun ```--keep-guessing``` parametresi ile hashlerin hangi şekilde kodlandığını tespit edebiliyoruz.

```yavuzkuk``` kelimesini <a href="https://bcrypt.online/">Bcrypt</a> sitesinden hashledikten sonra, Hashcat ile hash tipini tespit etmek istediğimde bir sonuç alamadım.

<figure><image src="../assets/hashcat/keepGues1.png"></figure>

<a href="https://www.tunnelsup.com/hash-analyzer/">Hash Analyzer</a> sitesini kullandığımda hash tipin doğru bir şekilde buldu.

<figure><image src="../assets/hashcat/hashAnalyzer1.png"></figure>


<a href="https://hashes.com/en/tools/hash_identifier">Hash Indetifier</a> sitesini kullandığımda hash tipin doğru bir şekilde buldu.

<figure><image src="../assets/hashcat/hashIdentifier1.png"></figure>

Diğer hashler üzerinden yaptığım denemelerde Hashcat SHA1,SHA256, SHA512, MD5 hashlerin tipini doğru tespit edebildi.

Hashcati kullanırken hangi hash türü üzerinde işlem yapacağımızı ```-m <sayi>``` parametresi ile vereceğiz.

<figure><image src="../assets/hashcat/hashmode.png"></figure>

Yukarıda bahsettiğim web sitelerinden hash tipini öğrenip, hashcat --help çıktısından öğrenebiliriz.

Örnek vermek gerekise MD5 tipinde bir hash için şu adımları yapabiliriz.

<figure><image src="../assets/hashcat/hashcatmode.png"></figure>

Gördüğünüz gibi ikinci komutumuzdan sonra MD5 hashi 0 değeri ile ifade ediliyor. hashcat komutunu kullanırken -m parametresi, ```-m 0``` olarak ayarlanır.

-----

Hashin tipini belirledikten sonra toolumuzu kullanmadan önce hashleri kırmak için kullanacağımzı bazı saldırı yöntemlerinden bahsedelim. Hashcati kullanırken bu saldırı yöntemlerinden birini ```-a``` parametresiyle bildirmemiz gerekiyor.

Bu atak yöntemlerini anlatırken örnek vermek amacıyla şu materyalleri kullanacağım; elimizde kırmak istediğimiz bir hash olduğunu ve hash.txt içinde olduğunu düşünün bunun yanında rockyou.txt dosyamız var.

### Dictionary Attack (Sözlük Saldırısı)

Bu saldırı yönteminde parametre olarak vereceğimiz rockyou.txt içinde bulunan kelimeleri, yine parametre olarak vereceğimiz hash türüne göre hashini alıp, çözümlenmesini istediğimiz hash ile karşılaştırdığımız bir saldırı türüdür.
```-a 0``` parametresi ile belirlenir.

```hashcat -m 0 -a 0 5f4dcc3b5aa765d61d8327deb882cf99 rockyou.txt```

Örnek vermek gerekirse:

Çözmek istediğimiz hash ```5f4dcc3b5aa765d61d8327deb882cf99```

Hash tipimizin MD5 olduğunu -m parametresiyle bildirilmiş durumda olduğunu düşünelim.

```
//rockyou.txt
hello   // --> 5d41402abc4b2a76b9719d911017c592
siberguvenlik // --> d66db4bef25092ec53e6197ddf7ad85a
password // --> 5f4dcc3b5aa765d61d8327deb882cf99
123456 // --> e10adc3949ba59abbe56e057f20f883e
```
Hashcat sırasıyla text dosyamızın içindeki değerleri tek tek MD5 ile şifreler ve çözmek istediğimiz hash ile karşılaştırır.
 
### Combinator Attack (Birleştirme Saldırısı)
Birleştirme saldırısı, iki wordlist dosyasını alır ve bu dosyalardaki kelimeleri birleştirerek hashleri kırmayı dener. ```-a 1``` parametresi ile belirlenir.

```hashcat -m 0 -a 1 hashes.txt rockyou.txt rockyou.txt```

### Brute Force Saldırısı/Mask Attack (Maskeli Saldırı)

Brute force saldırısı, şifrelerin denenebilecek tüm olasılıkları sistematik olarak deneyerek kırılmaya çalışıldığı bir yöntemdir. Bu yöntemde, her olası kombinasyon sırasıyla denenir ve şifre bulunana kadar devam edilir.

Mask attack, brute force saldırısının daha gelişmiş ve optimize edilmiş bir versiyonudur. Mask attack, belirli bir desen veya kalıba dayalı olarak şifrelerin denenmesini sağlar. Bu yöntem, saldırıyı daha verimli hale getirir. Kayıt oladuğunuz sitelere dikkat ettiyseniz, bazı sitelerde büyük harf ve özel karakter zorunluluğu vardır. 

Kullanıcılar genel olarak şifrelerin ilk harflerini büyük harfle başlar orta kısımlarda özel karakter kullanıp en son karakterlerde ise sayısal karakter kullanırlar. Buna özel bir desen hazırlayarak saldırılar gerçekleştirilebilir.

```-a 3``` parametresi ile belirlenir.


Mask attack sırasında kullanılabilecek bazı temel karakter setleri şunlardır:

- ?l: Küçük harf (a-z)
- ?u: Büyük harf (A-Z)
- ?d: Rakam (0-9)
- ?s: Özel karakterler
- ?a: Tüm karakter seti (küçük harf, büyük harf, rakam, özel karakterler)
- ?b: Binary karakter seti (tüm ASCII karakterler)

```hashcat -a 3 -m 0 hash.txt ?u?l?l?l?l?s?s?d?d -o cikti.txt```



### Hybrid Attack (Hibrit Saldırı)
Hibrit saldırı, sözlük ve maske saldırısını birleştirir. Örneğin, wordlist'teki kelimelerin sonuna sayılar ekler.
```-a 6``` parametresi ile belirlenir.

```hashcat -m 0 -a 6 hashes.txt rockyou.txt ?d?d```

-----

Hashcatin nasıl kullanılacağını öğrendik, öğrendiklerimizle 2 tane örnekler yapalım.

İlk öğreniğimizi Dictionary Attack ile yapmak istiyorum. Wordlist olarak rockyou kullanacağım. 

Kırmak istediğimiz hash: ```d177b4d1d9e6b6fa86521e4b3d00b029```

Bu hashin hangi tipte olduğunu bilmiyoruz. Yukarıda gösterdiğim sitelerden ya da Hashcat içinde bulunan ```--keep-guessing``` parametresi ile hash tipini öğrenmeye çalışabiliriz. 

<figure><image src="../assets/hashcat/exampleHash.png"></figure>

Hash tipimiz SHA2-512 olduğunu öğreniyoruz. Hashcat toolu ile -m parametresine hangi değeri vermemiz gerekiyor onu öğrenmeliyiz. Bu yüzden ```hashcat --help | grep SHA2-512``` komutunu çalıştırıyoruz. 

<figure><image src="../assets/hashcat/helpMd5.png"></figure>

-m parametremizi de öğrendiğimize göre artık hash işlemlerine başlayabiliriz.

```hashcat -m 0 -a 0 d177b4d1d9e6b6fa86521e4b3d00b029 rockyou.txt``` komutuyla saldırımız başlıyor.


<figure><image src="../assets/hashcat/result.png"></figure>


Son örnek olarak Mask Attack yöntemini kullanacağız. Vereceğimiz desen ise şu şekildedir:

B: Büyük karakter
K: Küçük karakter
Ö: Özel karakter
S: Sayısal karakter
```BKKÖSS```

Kırmak istediğimiz hash değeri: ```f00b6af455ad1934568b94ea7284bfa600594de8c3865e4fdb7cb1b12ab2f93677f9c62388016b87280e11807296a081836dd3fa4a77e6e1ce019f326eb4c120```



Öncelikle hash türümüzü öğrenmemiz lazım. Tekrardan söylüyorum yukarıda bahsettiğim siteleri kullanabiliriz.

<figure><image src="../assets/hashcat/secondQuest.png"></figure>


Hash türümüzü öğrendikten sonra komutumuzu yazabiliriz.

```hashcat -m 1700 -a 3 f00b6af455ad1934568b94ea7284bfa600594de8c3865e4fdb7cb1b12ab2f93677f9c62388016b87280e11807296a081836dd3fa4a77e6e1ce019f326eb4c120 ?u?l?l?s?d?d```

<figure><image src="../assets/hashcat/secondAnswer.png"></figure>

Bu işlemler sonucunda şifreye ulaşıyoruz.


> Bu yazı [*Yavuz Kuk*](https://www.linkedin.com/in/yavuzkuk/) tarafından hazırlanmıştır.