# Telnet


Telnet, ağ üzerinden başka bir bilgisayarın terminaline bağlanmak için kullanılan bir uygulama katmanı protokolüdür. Kullanıcılar, Telnet kullanarak uzak bir bilgisayara giriş yapabilir, bu bilgisayarda programlar çalıştırabilir ve sistem yönetim görevlerini uzaktan gerçekleştirebilirler.

Telnet protokolü, TCP/IP protokol ailesinin bir parçasıdır ve genellikle TCP port 23 üzerinden çalışır. 

Ancak, Telnet'in en büyük dezavantajı, tüm iletişimin şifrelenmemiş düz metin olarak gerçekleşmesidir. Bu durum, Telnet bağlantılarını saldırılara karşı savunmasız hale getirir; çünkü kötü niyetli kişiler ağ trafiğini izleyerek kullanıcı adı ve şifre gibi hassas bilgileri ele geçirebilir.

Telnet ile istediğimiz portu test etmek için kullanabiliriz. Herhangi bir port bilgisi vermezsek default olan 23 portundan bağlanmaya çalışır.

## Telnet'in Çalışma Şekli

Telnet protokolü oldukça basittir. Bir kullanıcı, bir Telnet sunucusuna bağlandığında, önce kullanıcı adı ve şifre istenir. Doğru kimlik bilgileri sağlandığında, kullanıcı uzak sistemin terminaline erişim kazanır. Kullanıcı bu terminal üzerinden komutlar çalıştırabilir ve sisteme müdahale edebilir.

```telnet 10.10.250.33```

Yukarıdaki IP adresine 23 portu üzerinden bağlanmaya çalışır. Doğru kullanıcı adı ve şifre işlemlerinden sonra kullanıcıya bir karşılama mesajı gösterilir ve ardından uzak sunucunun komut istemine erişim sağlanır.


Telnet, farklı portlara bağlanarak o port üzerinden sunulan hizmetlerin çalışırlığını ve yanıtını test etmek için kullanılabilir.

```telnet 10.10.250.33 21```

Ancak, Telnet'in güvenlik açısından zayıf olduğunu testlerde kullanırken dikkatli olmanız gerektiğini unutmamak önemlidir. Günümüzde, çoğu sistem yöneticisi Telnet yerine SSH kullanmayı tercih etmektedir. SSH, Telnet’in sunduğu uzaktan terminal erişimini güvenli bir şekilde sunar. Telnet'in yerine SSH kullanarak hem verilerinizin gizliliğini koruyabilir hem de sisteminizi saldırılara karşı daha güvenli hale getirebilirsiniz.