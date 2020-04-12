---
layout: post
title: Uygulama Lisans ByPass - Seviye 2
author: Halil Han Badem
---

Herkese selamlar,
**reverse** konusunda ilerlemeye devam ediyoruz. Bundan önce **login bypass** nasıl edilir onu yazmıştık. Birkaç şey öğrenmeye çalışmıştık. Bahsi geçen makalede kendi yazdığımız uygulama üzerinden gitmiştik. Bu yüzden senaryoyu biliyorduk. Neyin nerede olduğunu, hangi kod yazılmış bilmemezlikten gelsek bile gerçek zevki vermiyordu doğrusu 😁 

Bahsi geçen makaleye [buradan ulaşabilirsiniz.](https://halilhanbadem.com/reverse-ile-login-bypass-seviye-1)

Yavaştan başlayalım...

### Duyuru 📢
Bu makalede emeği geçen **byclone**'a teşekkür ederim.

### Uygulama ve Kullanılacak Toollar 🛠️
Reverse için **OllyDBG** kullanacağız. Saldırıyı yapacağımız uygulama ise **Paradox Converter** uygulaması. 

### Peki Neden Bu Uygulama ❓️
Paradox veritabanı dönüştürme ile ilgili bir çalışma gerekiyordu. Bunun için toollara bakarken bu uygulamayı gördüm ve en fazla 50 kayıt çıktı verdiğini tespit ettim. Sonrasında acaba bunu kaldırabilir miyim düşüncesi ile analiz ettim.

### Uygulama Analizi 👾
İlk olarak uygulamamızı indiriyoruz. Uygulamamızı açtığımızda bu ekran karşımıza geliyor. 

![](https://i.hizliresim.com/r0r9yN.png)

Ekranda bulunan "**Enter registration code**" butonuna tıklayarak rastgele denemeler ile hata mesajı yakalayacağım. Peki bunu neden yapıyorum. Şundan dolayı; string analizi ile text parçası arayacağız. Böylelikle herhangi bir hata mesajı aldığımda Ollydbg ile uygulama analizinde hata mesajını aratıp ilgili adrese ulaşmak isteyeceğim. Rastgele şeyler yazarak hata mesajı var mı yok mu bakıyorum.

![](https://i.hizliresim.com/GZVzG6.png)

şeklinde yazıp **OK** dediğimde herhangi bir tepki vermedi. O zaman trial modda devam edip uygulamada ne var ne yok bakalım...
 
![](https://i.hizliresim.com/NLPE25.png)

Next.. next diyerek ilerlediğim zaman 50 kayıt ile sınırlandıracağını belirtiyor. Böylelikle **Evet** seçeneğine tıkladığımız zaman bu kez 1000 kayıt varsa sadece 50 kayıt alacaktır. Bizimde amacımız bu engeli kaldırmak. OllyDBG'e geçerek işlemleri yapmaya başlayalım. Tabi hemen öncesinde uygulamada bulunan bir bug'a denk geldim. Yukarıdaki soru mesajına evet de deseniz hayır da deseniz işlemi gerçekleştiriyor...

OllyDBG aracına uygulamayı **attach** ettim. 

![](https://i.hizliresim.com/5NJVnj.png)

Şimdi analiz yapıp referans **string** değerlerde mesaj avına çıkacağız. Analiz yapmak için CTRL+A kısayol tuşunu kullanabilirsiniz veya aşağıdaki menüden de analizi yapabilirsiniz.

![](https://i.hizliresim.com/r0r9XN.png)

Analiz ettikten sonra aşağıda kaç fonksiyon, kaç prosedür hepsinin sayısı vs. çıkmakta. 

![](https://i.hizliresim.com/r0r943.png)

Şimdi ise referans string değerlere bir göz atalım. Sağ tık yapıp aşağıdaki menüye tıklayarak string tanımlarını gözlemliyoruz.

![](https://i.hizliresim.com/00dD3L.png)

Sonrasında ise yavaş yavaş göz atıyoruz... Biraz aşağıya doğru gittiğimde hata mesajını yakalıyoruz. Aslında o kadar satırı scroll ile aşağıya gelmek pek mantıklı değil. Hızlı sonuca ulaşmak için **Sağ Tık -> Search For Text** diyerek ilgili metni aratabilirsiniz. Hata mesajını işaretledim.

![](https://i.hizliresim.com/yGQ4kN.png)

Biraz daha aşağı indiğimde bu kezde onay yani "Kaydınzı başarılı" mesajını yakalıyorum. Onu da aşağıdaki resimde görebilirsiniz. Bu bizim işimize yarayacaktır. Zira sebebi ise ilgili adrese üzerine çift tıklayarak ulaşabilmemiz.

![](https://i.hizliresim.com/1p5dR5.png)

İlgili adrese çift tıklayarak geldik. Şimdi şu soru aklınızda takılabilir neden "50 kayıt" uyarısına gitmedik de buna geldik. Düşüncemiz burada müdahale ile tüm uygulamayı lisanslamak. Diğerinde büyük ihtimalle sadece mesajı bypass edecektik. Bu bizim için gereksiz olurdu.

![](https://i.hizliresim.com/3O0odp.png)

Buraya geldikten sonra yukarıdaki komutlara bir bakalım... 
Biraz yukarı doğru geldiğimde **0040B89A** adresinde `JE` yani bir şart görüyorum. Hop! Hemen bunu `JNE` komutuna çevirerek herhangi bir şey girsek bile lisanslamış olalım. Peki bunun mantığı nedir?

![](https://i.hizliresim.com/6DOndE.png)

### 80x86 komut setinde JE ile JNE farkı? 
 
`JE` bir şart içerir. Bu şart doğru ise bunu yap anlamına gelmektedir. `JNE` veya eş değeri olan `JNZ` komutu ise bu şart yanlış ise bunu yap demektir. Yani biz yanlış bir veri girdiğimizde mantıken doğru olan keyi algılamış gibi bizi lisanslaması gerekmekte. 80x86 komut seti ile bu tip müdahalelerde bulunabilirsiniz.

Şimdi biz bu komutu JNE yaparak bypass etmeye çalışacağız bakalım sonuç bize ne gelecek. İşlem adımlarımız;

   1. 0040B89A adresine gelin
   2. Çift tıklayın. 
   3. Açılan pencerede `JE` komutunu `JNE` olarak değiştiriniz.


Kısaca;

![](https://i.hizliresim.com/odv7vQ.png)

bu resmi şu şekilde değiştiriniz...

![](https://i.hizliresim.com/1p5d5Y.png)

**OK** dedikten sonra işlemimizi tamamlamış olduk şimdi ise uygulamamızı çalıştıralım...

![](https://i.hizliresim.com/JV121W.png)

İlgili satırı değiştirdik sonrasında ise uygulamamızı derleyerek değişiklikler gerçekleştirdik. Şimdi test edelim ne oldu ne olmadı... yine rastgele bir key girerek şansımızı deneyelim.

![](https://i.hizliresim.com/r0r9l1.png)

Random olacak şekilde bir key giriyorum. Sonrasında ise keyi doğru olarak kabul ediyor.

![](https://i.hizliresim.com/7Bzv0a.png)

Artık lisanslı mıyız? Test edelim bakalım 50 adet sınırını atlatabildik mi...

![](https://i.hizliresim.com/P79ML7.png)

Sinir bozucu... düşünmemiz lazım burada yani if bloğu ile pek alakalı değil. O if bloğuna başka bir prosedür veya fonksiyondan bir kontrol gerçekleşiyor. Sonrasında o if bloğuna geliyoruz. Bu yüzden biraz daha incelememiz lazım. Denemeden önce şu aklınıza takılabilir. Neden halen 50 limitini bypass etmiyoruz? Harbiden neden etmiyoruz? Edelim...

![](https://i.hizliresim.com/DOZJnl.png)

komutu göründüğü üzere düzenliyorum. Böylelikle 50 limit uyarısını da halletmiş olduk. Nasıl geldiğimi soracak olursanız eğer yine yukarıda yaptığım gibi ilk olarak string araması yapıp sonrasında ilgili adrese geldim. Sonrasında ise ilgili adresin yukarısında if şartını ters çevirdim. Derleyip devam edelim bakalım ne olacak...

![](https://i.hizliresim.com/JV12Pj.png)

Uyarı gitti fakat yine 50 limit ile çıktı veriyor. Neymiş bu 50 limit mübarek! İşte bundan sonrası yukarıda bahsettiğim üzere **byclone**'ın yardımı ile ilerleyeceğiz. İlk aşamada biraz daha kontrol ediyoruz. **0040B8C8** adresinin yukarısında bulunan bir call isteği gözüme çarpıyor. Bu tip durumlarda yapmanız gereken **breakpoint** koymak. Sonrasında ise bu **breakpointi** takip etmek. **Call** isteğinin içine girmek için **debug** esnasında **F7** tuşunu kullanmanız gerekmektedir. Bu tuş ile beraber call isteğinin detayını girip oradaki ilgili adresi düzenlememiz gerekecek.


### İyi de Breakpoint nedir? ❓️
Google'a yazmayı üşenenler için direkt Google sonucunu alıntılıyorum...

> Yazılım geliştirmede, kesme noktası, hata ayıklama amacıyla kullanılan bir programdaki kasıtlı bir durma veya duraklatma yeridir. Ayrıca bazen basit bir duraklama olarak da adlandırılır. Daha genel olarak, bir kesme noktası, yürütme sırasında bir program hakkında bilgi edinmenin bir yoludur.

Daha fazlası için kendiniz araştırabilirsiniz. Şimdi olayımıza dönelim... Adımlarımızı tek tek **OllyDbg** içerisinde gerçekleştiriyoruz. **Breakpoint** eklemek için ilgili adresin üstüne gelip **F2** tuşuna basın. Adresin yazıldığı kolon kırmızı renkte olması gerekiyor. Bu breakpointin başarılı bir şekilde eklendiğini söylemekte. Dilerseniz **Alt+B** tuşu ile tüm uygulamada kaç **breakpoint** varsa hepsini görebilirsiniz. Böylelikle sağda solda herhangi bir yerde **breakpoint** kalmış ise kolaylıkla kaldırabilirsiniz. İlgili adımları tek tek resimler ile gösterelim.

![](https://i.hizliresim.com/5NJVZA.png)

Başarılı ise bu şekilde gözükmesi gerekir:
![](https://i.hizliresim.com/86B1qd.png)

Şimdi breakpointi koyduk. Yapmamız gereken uygulamayı çalıştırdıktan sonra yine rastgele key girmek. Bizi tutup bir kontrole sokması lazım. Bu aşamada **call** işlevinin detayına inip ilgili birimi değiştirmemiz gerekmekte. Başlıyoruz...

![](https://i.hizliresim.com/va940D.png)

Rastgele yine girdik ve OK tuşuna bastık. Uygulama breakpoint noktasında durdu.

![](https://i.hizliresim.com/1p5d9b.png)

Durduğunu bu şekilde anlayabilirsiniz... F7 tuşu ile detaya inmeye devam ediyorum. Call isteğinin ayrıntısını aşağıya bırakıyorum

```
004166B4  /$ 55             PUSH EBP
004166B5  |. 8BEC           MOV EBP,ESP
004166B7  |. 56             PUSH ESI
004166B8  |. 57             PUSH EDI
004166B9  |. 8B7D 10        MOV EDI,DWORD PTR SS:[EBP+10]
004166BC  |. 8B4D 08        MOV ECX,DWORD PTR SS:[EBP+8]
004166BF  |. 8B75 0C        MOV ESI,DWORD PTR SS:[EBP+C]
004166C2  |> 83FF 04        /CMP EDI,4
004166C5  |. 7C 34          |JL SHORT pxcnv.004166FB
004166C7  |. 8A01           |MOV AL,BYTE PTR DS:[ECX]
004166C9  |. 8A16           |MOV DL,BYTE PTR DS:[ESI]
004166CB  |. 3AD0           |CMP DL,AL
004166CD  |. 75 2C          |JNZ SHORT pxcnv.004166FB
004166CF  |. 8A41 01        |MOV AL,BYTE PTR DS:[ECX+1]
004166D2  |. 8A56 01        |MOV DL,BYTE PTR DS:[ESI+1]
004166D5  |. 3AD0           |CMP DL,AL
004166D7  |. 75 22          |JNZ SHORT pxcnv.004166FB
004166D9  |. 8A41 02        |MOV AL,BYTE PTR DS:[ECX+2]
004166DC  |. 8A56 02        |MOV DL,BYTE PTR DS:[ESI+2]
004166DF  |. 3AD0           |CMP DL,AL
004166E1  |. 75 18          |JNZ SHORT pxcnv.004166FB
004166E3  |. 8A41 03        |MOV AL,BYTE PTR DS:[ECX+3]
004166E6  |. 8A56 03        |MOV DL,BYTE PTR DS:[ESI+3]
004166E9  |. 3AD0           |CMP DL,AL
004166EB  |. 75 0E          |JNZ SHORT pxcnv.004166FB
004166ED  |. 83EF 04        |SUB EDI,4
004166F0  |. 83C1 04        |ADD ECX,4
004166F3  |. 83C6 04        |ADD ESI,4
004166F6  |. 83FF 04        |CMP EDI,4
004166F9  |.^7D C7          \JGE SHORT pxcnv.004166C2
004166FB  |> 85FF           TEST EDI,EDI
004166FD  |. 75 04          JNZ SHORT pxcnv.00416703
004166FF  |. 33C0           XOR EAX,EAX
00416701  |. EB 19          JMP SHORT pxcnv.0041671C
00416703  |> 8A01           /MOV AL,BYTE PTR DS:[ECX]
00416705  |. 8A16           |MOV DL,BYTE PTR DS:[ESI]
00416707  |. 3AD0           |CMP DL,AL
00416709     75 05          JNZ SHORT pxcnv.00416710
0041670B  |. 41             |INC ECX
0041670C  |. 46             |INC ESI
0041670D  |. 4F             |DEC EDI
0041670E  |.^75 F3          \JNZ SHORT pxcnv.00416703
00416710  |> 33C9           XOR ECX,ECX
00416712  |. 8AC8           MOV CL,AL
00416714  |. 33C0           XOR EAX,EAX
00416716  |. 8AC2           MOV AL,DL
00416718  |. 2BC8           SUB ECX,EAX
0041671A  |. 8BC1           MOV EAX,ECX
```


![](https://i.hizliresim.com/Lvr3Jz.png)

İşaretlemiş olduğum yer dikkatimi çekiyor bir çok şart mevcut. 

![](https://i.hizliresim.com/r0r9qz.png)

Garip bir şekilde orayı atladı. Devam ediyoruz bakalım ne çıkacak...

![](https://i.hizliresim.com/7BzvOr.png)

Ok ile gösterdiğim şarta geldiğinde alttaki kodu atlayıp direkt bulunduğu yere geldi... Bende bu kodu ters çevirerek ilgili kodun çalışmasını istiyorum. Bu kod ile beraberinde doğru keyi girmiş olduğumuzu ve ilgili atamalar vs. **var ise** yapmasını istiyorum. Öğrenmiş olduğumuz ters çevirme işlemi..
```

004166FB  |> 85FF           TEST EDI,EDI
004166FD  |. 75 04          JNZ SHORT pxcnv.00416703
004166FF  |. 33C0           XOR EAX,EAX
00416701  |. EB 19          JMP SHORT pxcnv.0041671C
00416703  |> 8A01           /MOV AL,BYTE PTR DS:[ECX]
00416705  |. 8A16           |MOV DL,BYTE PTR DS:[ESI]
00416707  |. 3AD0           |CMP DL,AL
00416709     74 05          JE SHORT pxcnv.00416710
0041670B  |. 41             |INC ECX
0041670C  |. 46             |INC ESI
0041670D  |. 4F             |DEC EDI
0041670E  |.^75 F3          \JNZ SHORT pxcnv.00416703
00416710  |> 33C9           XOR ECX,ECX
00416712  |. 8AC8           MOV CL,AL
00416714  |. 33C0           XOR EAX,EAX
00416716  |. 8AC2           MOV AL,DL
00416718  |. 2BC8           SUB ECX,EAX
0041671A  |. 8BC1           MOV EAX,ECX
```

![](https://i.hizliresim.com/P79MRb.png)

Bu sefer `JNE` komutunu `JE` komutuna çeviriyorum.

![](https://i.hizliresim.com/gPaoNQ.png)

Çevirme işlemi tamamdır. Şimdi ise birkaç kez F7'ye bastığımda beni döngüye alıyor. Başa dönüyorum. Düzelttiğimiz yere tekrar geçecek. Bakalım kodu işleyecek mi?

Bahsettiğim kod bloğu: 

![](https://i.hizliresim.com/Z54dr0.png)

Evet başarılı bir şekilde kodu işliyor şimdi F7'ye basmaya devam edelim.

![](https://i.hizliresim.com/nbnrv1.png)

Biz devam ettikçe belirli kontrolleri sağlayacak... Sağladıktan sonra bizi yine aynı noktaya attı.

![](https://i.hizliresim.com/86B8mV.png)

Burada heyecanlı bekleyiş var... Bakalım onay mesajını verip 50 limitinden kurtaracak mı?

![](https://i.hizliresim.com/nbnEGg.png)

Onay aldık ilerleyelim...

![](https://i.hizliresim.com/P79QEQ.png)

Mutlu son.. Artık tüm kayıtları alabiliyoruz. Şimdi neler yaptık ona bakalım.

### Neler Yaptık 👾
İlk aşamada uygulamamızı analiz ettik. Analiz sonucunda herhangi bir hata mesajı vermediğini (Geçersiz lisans gibisinden) gördük. Bu durumda uygulamamızı **Ollydbg** ile analiz edip **string** araması yaptık. Sonrasında bu arama ile Doğru lisans uyarısını ve 50 limit uyarısını bulduk. Bu iki adreste şartları ters çevirerek bir şekilde işin içinde çıkmaya çalıştık ama olmadı.. Sonrasında biraz daha inceleme ile şartların üstünde bulunan **call** isteğini gördük. Burada **breakpoint** ile **F7** kısayol tuşu ile detaya indik. Sonrasında içerisinde bulunan **if** şartının bir kod bloğunu işlemediğini gördük ve denemek için komutu tersine çevirdik. **F7** ile devam edip bizi bir süre döngülere soktuktan sonra onay mesajını verdi. En sonunda ise tüm kayıtlarımızı **convert** etmiş olduk. 


### Peki şimdi suç mu işledik? ❓️
Sadece zaafiyeti ortaya çıkarmak ve bu tip zaafiyetlerde ne gibi işlemler yapılacağını açıklığa kavuşturmuş olduk. Amacımız suç işlemek değildir...


### Son
Eklemek istediğim bir şey var mı diye düşünüyorum ama pek yok. Sadece bunu dökümanı öğrenmek için kullanınız. Ek olarak bu yöntemden farklı olarak birçok yöntem mevcut. Herkes aynı yoldan reverse veya cracking yapacak değil bu yüzden sizlerin(bu işin ehli veya farklı algoritma üzerinden gidenler) farklı algoritması var ise aşağıya belirtebilirsiniz. sürçü lisan ettiysem affola. Yanlış bir bilgilendirme varsa lütfen bildiriniz.

Bir başka makalede görüşmek dileğiyle,
iyi çalışmalar dilerim. 

