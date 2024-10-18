# Race Condition

Race Condition, Türkçe çevirisi olarak Yarış Hali olarak geçiyor. İsmindende az çok anlayabileceğiniz üzere sistemde bir yarış durumu var ve bu durumdan dolayı zafiyet ortaya çıkıyor.

Temel olarak anlatmak gerekirse sisteme gelen isteklerin aynı anda aynı değişken veya aynı kaynağa erişmesiyle ortaya çıkar.

Aynı anda aynı kaynak üzerinde değişiklik yapılmasına collision (çakışma,çarpışma) denir. İstekler çok kısa sürede collision durumunda bulunur. İsteklerin çarpıştığı anlık duruma ise race window denir.

Race window, uygulamanın veri işlemesi sırasında birden fazla işlemin aynı anda değişiklik yapmaya çalıştığı kritik bir bölgedir. Eğer işlemler bu kritik bölgeye girmeden düzgün şekilde sıralanamazsa, çakışma gerçekleşir. Bu tür kritik bölgelere erişim genellikle kilit (lock) veya senkronizasyon mekanizmaları ile kontrol edilir. Eğer gerekli kontroller sağlanmazsa birden fazla istek atılarak sistemin olağanın dışında çalışması sağlanabilir.

Temel olarak zafiyetin nasıl çalıştığını anladı
