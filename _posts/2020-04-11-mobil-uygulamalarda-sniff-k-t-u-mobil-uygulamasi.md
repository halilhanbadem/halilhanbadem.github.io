---
layout: post
author: Halil Han Badem
title: Mobil Uygulamalarda Sniff - K.T.Ü Mobil Uygulaması
---

### Başlangıç <br>
Selamlar, yazı dizimizde farklı farklı uygulamaları(mobil/pc) sniff yöntemi ile izleyip elimize nelerin geçeceğine bakacağız. Bunları yaparken hem öğrenip hem de varsa uygulamalarda bulunan hataları gözlemleyeceğiz.

### Uyarı 

> Bu eylem Karadeniz Teknik Üniversitesine bildirilmiştir. Eylem herhangi bir sorun teşkil etmediği cevabı alındığından yazıda uygulamaya yer verilmiştir. Web servisleri kullanmaya kanunen izin verilmemektedir. Klon uygulama yapmak ve kullanıma sunmak, materyalleri kullanmak idari merciiler tarafından incelenir eğer uygun görülürse hapis cezasına kadar giden hukuki yargılamalar ile sonuçlandırılabilir. Uygulama bazlı durumlar için mail adresimiz ile iletişime geçebilirsiniz: halilhanbadem[at]protonmail[dot]com 


 ### Gerekli Uygulamalar
 K.T.Ü Mobil Uygulaması: [KTÜ Mobil](https://play.google.com/store/apps/details?id=ktu.mobil) <br>
 Packet Capture Uygulaması: [SSL Capture](https://play.google.com/store/apps/details?id=app.greyshirts.sslcapture)

Uygulamaları indirip kurduktan sonra Packet Capture uygulamamıza giriyoruz. Uygulamamızın temel amacı kaynaktan giden verileri izleyerek bunları kullanıcının önüne getirtmek. Bu amaçla giden veriyi kolaylıkla görebiliriz. İsterseniz bir proxy oluşturarak bilgisayarınızda bulunan herhangi bir sniffer ile bu işlemi de yapabilirsiniz. Tek yapmanız gereken aynı wifi ağına bağlı olmak. Bunun sebebi proxy üzerinden geçecek olan dataları belirli bir adresten görüntüleyebilmenize sebep olacaktır. Mobil içinde vermiş olduğum uygulama aynı işlemleri yapmakta. Kaynak yani sizin bilgisayarınız, telefonunuz, tabletiniz veya herhangi bir teknolojik ürününüzden(internete bağlı) karşı tarafa giden verileri gitmeden önce paketleri görüntülemesidir. 


### Peki bu bizim ne işimize yarıyor?   <br>
  
Mobil uygulamaların temelde 2 güvenlik testi vardır. Bu temellerden doğan mirasçıl testler çıkmıştır. Sniff ve reverse. Sniff ile herhangi bir web servise bağlanıp doğrulama yapan veya bu doğrulamadan sonra veri çeken veya herhangi bir siteden veri çeken uygulamaların hangi adreslere hangi verileri gönderdiği saptanır. Böylelikle saldırganların oluşturabileceği zafiyetler hesaplanır. 


### Peki bunun önlemi mevcut mu?  <br>
 
Ne kadar çok saldırı, o kadar çok savunma mekanizmasının oluşmasına sebep olur. Bu tip durumlar için en sık kullanılan yöntem SSL sertifikası ile veriyi şifreleyerek karşı tarafa göndermektedir. Böylelikle uygulamada veri şifrelenir ve gönderildiği için paketler şifreli olarak gelir. SSL'in en temelde AES şifreleme teknolojisini kullandığı için o şifreyi çözümleyebilmek için bir anahtara ihtiyacınız olur. Bu sebeple veri %99 çözümlenemez. Bir diğer yöntem ise SSL'i daha güçlü hale getirerek içerisine güvenlik numaraları enjekte etmek. Böylelikle çok güç olsa da AES anahtarının ele geçirilmesi durumunda birde güvenlik kodları engeline takılacaktır. Bunun yanı sıra uygulamanın web servise bağlanması için proxy adresi kullanması, web servisin gelen her isteği onaylamaması gibi bir takım güvenlik önlemleri uygulanabilir.


### Eylem vakti

        1) Packet Capture uygulamasını açalım.

![](https://i.imgyukle.com/2019/10/08/ECvKD1.png)

        2) Ekranda bulunan sağ yeşil tuşa basarak izlenimi aktif yapmaya çalışıyoruz.
        
 ![](https://i.imgyukle.com/2019/10/08/ECvWgG.png)       
 
        3) Uygulamaya ait olan bir SSL ile ağımızı izlemek istediğini belirtiyor, evet diyerek onaylıyoruz.
        
![](https://i.imgyukle.com/2019/10/08/ECvqbI.png)

        4) İzlenim açıldı. Dikkat ederseniz SSL yazıyor yanında yani şifrelenmiş. Tıklasanız bile boş data dönmüştür. 
        
![](https://i.imgyukle.com/2019/10/08/ECve8S.png)

🔄 Bu genel trafiği dinleyen bir yoldu. Birde sadece uygulama dinlemek istiyorsak önce kırmızı tuşa basarak izlenimi sonlandırıyoruz. Sonrasında sol yeşil tuşa basarak uygulama seçimine gidiyoruz.

![](https://i.imgyukle.com/2019/10/08/EC1CFN.png)

Seçimi yaptıktan sonra aşağıda ilk açtığımız üsteki ise sadece seçtiğimiz uygulamayı izleyen alan olarak belirleniyor. Üsteki bölüme tıklayıp uygulamayı küçültüyoruz.

![](https://i.imgyukle.com/2019/10/08/EC1unP.png)

Diğer işlem K.T.Ü mobil uygulamasına girmek olacaktır. 

![](https://i.imgyukle.com/2019/10/08/EC1Id0.png)

Uygulamaya giriş yaptıktan sonra random bir şekilde giriş yapmayı deniyoruz.

![](https://i.imgyukle.com/2019/10/08/EC1xjq.png)

Bunu yaptığımızda sunucuya bir veri gidecektir ve bize bir geri dönüşü olacaktır. Geri dönüş tabi ki öğrenci bulunamadı oluyor. SSL sertifika koruması olmadığı için rahatlıkla verileri görebiliyoruz. Gelen sonuçlar:

![](https://i.imgyukle.com/2019/10/08/ECKZ5U.png)

Web servis doğrulama (auth) bilgilerine ulaşmış olduk. Web servis adresine kolaylıkla ulaşabilirsiniz. Bunun için host + post değerlerini birleştirerek browser üzerinde link şeklinde açarsanız hiçbir sorun kalmayacaktır. Hem web servise hem de web serviste rahatlık sorgulama yapabileceksiniz. Peki bunun bir diğer yolu nedir?


### APK Decompiler   

K.T.Ü Mobil uygulamasını apk şekli ile bilgisayarımıza indirelim. Geçerliliği halen devam ediyorsa buradan indirebilirsiniz.

[İndirmek için tıklayınız](https://d-12.winudf.com/b/apk/a3R1Lm1vYmlsXzhfZjFhNWYxNGE?_fn=S1TDnCBNb2JpbF92MS4wLjdfYXBrcHVyZS5jb20uYXBr&_p=a3R1Lm1vYmls&k=78c1af35fc598291f25c4797576098835d9dd178)

Eğer 404 veya ulaşma hatası alıyorsanız Google üzerinden kendinizde indirebilirsiniz. İndirme işlemi tamamlandıktan sonra APK decompile etmek için gerekli aracımızı açıyoruz. Kullandığım araç APK Easy Tool. İsterseniz farklı tool kullanabilirsiniz. 

[img]https://i.imgyukle.com/2019/10/08/ECegX1.png[/img]

İlk olarak decompiler uygulamamız ile indirmiş olduğumuz APK dosyasını açıyoruz.

[img]https://i.imgyukle.com/2019/10/08/ECeheI.png[/img]

Sonrasında Decompiler diyerek işlemimizi başlatıyoruz.

[img]https://i.imgyukle.com/2019/10/08/ECecnA.png[/img]

Bu işlem tamamladıktan sonra uygulamanın Decompiler ettiği dosyaların olduğu klasöre gidiyoruz

[img]https://i.imgyukle.com/2019/10/08/ECejMH.png[/img]

Bazen ayrı şekilde yani kütüphane vb. bir biçimde bağlantılar olabilir. Bu yüzden MainActivity dosyası yerine başka dosyalarda da önemli bilgiler yer alıyor. Biz tabi direkt MainActivity dosyasına baktığımızda tekrar bilgiler elimize gelmiş oluyor.

[img]https://i.imgyukle.com/2019/10/08/ECeOBU.png[/img]

> Blurlu alanlar bilgilerin içerdiği alandır. Sitemiz hiçbir sisteme zarar vermeden pentest testlerinin paylaşımını desteklemektedir. 

Görmüş olduğunuz üzere iki farklı yöntemle de bilgilere erişmiş olduk. Durum çok önceden gerekli merciilere bildirilmiş olup, bilgilendirme yapılmıştır. 

### Uyarı ⚠️  <br>

Malumunuz Türkiye'de yaşamaktayız ve bilinçsiz bir biçimde hacking meraklısı gencimiz bulunmakta. Düşünme yeteneğine henüz erişmemiş, tecrübesiz bazı arkadaşlar bu tür açıkları kötü niyetle kullanabilir. Her yazı sonunda bu tip uyarılar ve neden bu uyarıları koyduğuma dair uygulama bazlı bir açıklama yapacağım. 


### Neden açıkları kötü niyetle kullanmamalıyız? ℹ️  <br>

Büyük kurum ve kuruluşların başlı başına bir siber güvenlik mekanizması veya bir log sistemi, kanuni haklar vb. geçerlilikleri mevcuttur. Google Play'de bulunan uygulamayı klonlayarak aynı uygulamayı markete koyma gibi bir düşüncede kurumdan önce önünüze Google Play politikaları çıkacaktır. Uygulamalar marketlere yüklenmeden gerçek insanlar (robot değil merak etmeyin) tarafından incelenmektedir. Bu sebeple birçok insanı yanıltmayı bu şekilde düşünemezsiniz. 

Bir diğer husus ise web servisi kullanmak. Bu risk kuruma bildirildiğinden dolayı herhangi bir saldırıda, saldırgana hukuki süreçler başlatarak -bu işlem kötü/iyi niyetli olsun fark etmeksizin- haklarını koruma hakkında sahiptir. Bir diğer zeki(!) ve parlak(!) kişiler bu işlemi VPN ile yapabileceğini düşünmektedirler. Unutulmamalıdır ki VPN yapısı sizi %100 gizlemez. İnternet sağlayıcısından yapılacak olan bir hukuki istek ile hangi cihazdan sorgu yaptığınıza bile ulaşılmaktadır. Lütfen bu dökümanları sadece öğrenmek için kullanınız. 

Teşekkürler,
İyi çalışmalar.
