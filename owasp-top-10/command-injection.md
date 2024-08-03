---
cover: >-
  ../.gitbook/assets/Default_A_dimly_lit_server_room_with_rows_of_humming_machines_1.jpg
coverY: 268.71651623390517
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

# Command Injection

Command Injection, bir uygulamayı çalıştıran sunucuda işletim sisteminde (OS)  yetkisiz komutlar çalıştırmasına olanak tanıyan bir saldırı türüdür. Genellikle uygulamanın ve verilerinin tamamen tehlikeye atılmasına olanak tanır.

Bu saldırı türü, genellikle bir uygulamanın kullanıcı girdisini doğru şekilde doğrulamadığı veya sınırlamadığı durumlarda ortaya çıkar. Saldırgan, bu eksik doğrulama veya sınırlamadan yararlanarak zararlı komutları uygulamaya sızdırabilir ve bu komutlar hedef sistemde çalıştırılabilir.

İşletim sistemi üzerinde kod çalıştırabileceğimiz için sistem hakkında bilgi toplayabiliriz.

### Command Injection zafiyetinin ortaya çıkma sebepleri:

* **Kullanıcı Girdisinin Doğrulanmaması**: Kullanıcıdan alınan girdinin doğrulanmadan veya sınırlanmadan işletim sistemi komutlarına dahil edilmesi.
* **Kötü Tasarım**: Yazılımın, kullanıcı girdisini doğrudan işletim sistemi komutlarına dahil edecek şekilde tasarlanması.
* **Eksik Girdi Temizleme**: Kullanıcı girdisinin zararlı içeriği temizlenmeden komutlara dahil edilmesi.

#### Injection çeşitleri

1. **Blind Injection :** Bu tür saldırı tipinde, payloadları test ederken uygulamadan doğrudan çıktı almadığımız senaryolardır. Payloadların başarılı olup olmadığını belirlemek için uygulamanın davranışlarını araştırmanız gerekecektir. Göndereceğimiz payloadın çıktısını göremeyeceğimiz için işlemin bitiş zamanı ile ilgili manipülasyonlarda bulunabiliriz. Ne demek oluyor bitiş zamanı manipülasyonu. `sleep` ve `ping` komutlarıyla işlemlerin bitişlerinin ertelenmesi sağlanır.
2. **Verbose Command Injection :** Bu tür saldırı tipinde ise girdiğimiz komutun çıktısını ekranda görebilir durumdayız. Bu tür saldırılarda genel olarak `ls` ve `whoami` gibi komutlar sıklıkla kullanılır.

Command Injection zafiyetini simüle etmek için [Try Hack Me Command Injection labı](https://tryhackme.com/r/room/oscommandinjection) üzerinden anlatım yapacağım.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Fotoğrafta da gördüğünüz gibi verdiğiniz bir IP adresine ping atıyor. Bu yapılan işlem için arkada bir ping komutu çalışıyor. Bizden aldığı IP değerini de ping komutunun sonuna ekliyor ve o adrese ping atıyor.

```
ping <IP adresi>
```

Bize sorulan soruda ise `/home/tryhackme/flag.txt` içindeki yazı isteniyor. Dosyayı okumak için `cat` komutu kullanacağız.

Temel olarak neleri kullanacağımızı gördükten sonra bunları nasıl kullanacağız onu konuşmamız lazım. Bizim öncelikle `ping` komutuna bir IP adresi vermemiz gerekiyor sonrasında `cat` komutuyla bizden istenen dosyayı okumamız gerekiyor.

Aynı anda birden fazla komut çalıştırmak için bazı ara karakterlere ihtiyacımız var. Komut satırında bu özellik için && karakteri kullanılır.Input alanına şöyle bir payload yazmamız lazım `127.0.0.1 || cat /home/tryhackme/flag.txt`, bu payload sonucunda oluşan komut satırı şu şekildedir:

<pre><code><strong>ping 127.0.0.1 || cat /home/tryhackme/flag.txt
</strong></code></pre>

Payloadı girdikten sonra istediğimiz dosyanın içeriğini okuyabiliyoruz.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Çeşitli senaryolarda çeşitli karakterler ve komutlar engellenir. Farklı komutlar kullanılarak engeller aşılabilir.
