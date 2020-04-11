---
layout: post
title: SQL Injection Login ByPass & Reverse ByPass - Seviye 3
author: Halil Han Badem
---

Herkese selamlar,
bu makalemiz biraz uzun olacak. İki farklı konuyu ele alacağız. Bu konuların biri **SQL injection** ile **login bypass** yapmak. Diğeri ise bu işlemi **reverse** ile nasıl yaparız onu ele alacağız. Peki uygulama nedir? Hangi platform? gibi soruları cevaplandıralım.

### Hangi Uygulama? Hangi Platform? ❓️ 
Uygulama bundan 4-5 sene önce **Delphi** programlamaya başladığım ilk zamanlara ait. Bu süre zarfında çokça yol katetmiştim fakat ilk zamanlar baya acemice uygulamalar yazdığım doğrudur. Dün arşive göz atarken dikkat ettiğim ve testleri yaptığımda olumlu sonuç aldığım tamammiyle acemice yazılmış uygulamayı ele alacağız. Windows işletim sisteminde çalışan masaüstü uygulamamız, **Access** veritabanını kullanmakta. İlk aşamada bizlere kullanıcı adı ve şifre sormakta. Zor değil istesek veritabanını açıp kullanıcı adı ve şifresine bakarız ama bizim amacımız bize/bana ait olmayan bir uygulama olsaydı ve **veritabanına** **erişim** iznim olmasaydı neler yapmayı düşünürdük onu anlamak. 


### Hangi Araçlar Kullanılacak? 🛠️  
Yine **ollydbg** kullanılacak. Ollydbg aracı ile ilgili geçmiş makalelerimize göz atabilirsiniz.

 Önceki makalelere aşağıdan göz atabilirsiniz: 
   [Uygulama Analizi ve Saldırı](https://yazilimtoplulugu.com/d/32)
   [Uygulamaya Müdahale Nasıl Yapılır?](https://yazilimtoplulugu.com/d/65)
   [Reverse ile Login ByPass - Seviye #1](https://yazilimtoplulugu.com/d/224)
   [Uygulama Lisans ByPass - Seviye #2](https://yazilimtoplulugu.com/d/426)


### Uygulamanın Analizini Yapalım 👓️ 
Uygulamamız **Delphi** dili ile yazılmış. Yukarıda bahsettiğimiz gibi **Access** veritabanı kullanılmış ve **FastReport** raporlama aracı kullanılmış. **Ini** dosyası ile bir takım bilgiler tutuluyor. Bu bilgiler ışığında büyük ihtimal uygulama başlangıçta bu dosyaya göre şekil alıyor.

![](https://i.hizliresim.com/XbAGj7.png)

Uygulamanın dosya yapısı yukarıda mevcut. Yani yukarıda bulunan ihtimalleri bu şekilde gözlemleyerek elde ettim. FastReport dememin sebebi **belgeler** klasöründe **fr3** uzantılı dosyaların mevcut olması. Diğer taraftan zaten **data** klasöründe **mdb** uzantılı veritabanı olmasından **Access** olduğu anlaşılıyor. 

![](https://i.hizliresim.com/bv31a8.png)

Uygulamayı açtığımda önüme bu şekilde bir **login** ekranı geliyor.  

![](https://i.hizliresim.com/dLpra4.png)

İlk aşamada rastgele bilgiler ile yine test ediyoruz. Bunu neden yapıyoruz benden bilmiyorum alışkanlık oldu. Sanki key arıyoruz 😅 Devam edelim... almış olduğumuz mesaj işimize yarayabilir. Bu yüzden bunu aklımızda tutalım. Şimdi biz direkt **reverse** ile işe başlayacağız. Bakalım bu adımımız doğrultusunda nelere denk geleceğiz.

### Ollydbg ile Başlayalım...  👨‍🔧 
İlk aşamada uygulamamızı olly ile açalım. Açtıktan sonra ise uygulamamızı olly üzerinden başlatalım. Sonrasında standart işlemleri yapacağız. **Code Analysis** ve **referans text** ile dönen hata mesajını aramak gibi bir takım temel arama çalışmaları yapacağız.

![](https://i.hizliresim.com/2OAdpE.png)

Öncelikle yukarıdaki adımlar ile kod analizi yapıyoruz. Bu adımları diğer makalelerde gösterdim evet... fakat amacım bu makaleyi ilk defa okuyan birisi için tekrardan anlatmak ve hatırlatmak.

![](https://i.hizliresim.com/BOYnjL.png)

Sonrasında referans string içerisinde arama gerçekleştiriyoruz. Bu aramada amacımız parametre dönen hata mesajını bulmak ve ilgili adresi bulup o adres çevresinde müdahale gerçekleştirmek.

![](https://i.hizliresim.com/Z546l3.png)

Bu menü seçeneği ile arama yapıyoruz.

![](https://i.hizliresim.com/odv5JX.png)

Standart olarak mesaj içeriğini aratıyoruz. Burada dikkat etmemiz gereken nokta Türkçe karakter olduğu için direkt olarak değilde **ASCII** karakter olarak verebilir. Bu yüzden arama tuşuna bastığımda herhangi bir sonuç elde edemedim. Bu bir sorun değil. Türkçe karakteriler içerisinde arama yaparak da deneyebilirdim ama büyük ihtimalle sonuç almayacaktım çünkü Türkçe karakter içeren bir set **string** olduğu için tümünü **ASCII** olarak yazdırmış olabilir veya daha farklı bir **formatta**. Şimdi ben son kez dönen mesajın başlığını aratarak bir şeyler bulmaya çalışacağım. Yani **Hata!** kelimesini aratacağım tabi bunu **Hata** olarak aratacağım.

![](https://i.hizliresim.com/lQlXAQ.png)

Arattık ve sonuç elde ettik. Tabi bu istediğimiz login bölümü değil. Nereden anlıyoruz peki? Şuradan anlıyoruz; bir sorgu görüyorum yukarıda, **Aboneler** tablosu ile ilgili bir sorgu var. Ben **login, kullanıcı** içeren bir sorgu arıyorum.

![](https://i.hizliresim.com/VQdOjB.png)

Bir kez daha arama yapmak istediğimde (**CTRL+L**)  yukarıdaki gibi **Hata** başlıklı Kitaplar tablosu ile ilgili bir sorgu gördüm. Ben aramaya devam ediyorum...

![](https://i.hizliresim.com/nbnd4N.png)

Evet, KULLANICI tablosu ile ilgili bir sorgu bulduk. **KADI** ve **SIFRE** adında iki alan ile işlem yapıyor. Bizim adres belli oldu. Biz bu taraflarda olta atacağız. 

![](https://i.hizliresim.com/va9G3A.png)

Çift tıklayarak adrese gidiyoruz. Yukarıda görüldüğü üzere **MessageBox** fonksiyonumuz mevcut. Hemen üstünde ise Hata başlıklı **unicode** biçimindeki aramada bulduğumuz **text** mevcut. 

![](https://i.hizliresim.com/1p5bRB.png)

Hemen üst tarafta ise bir **JG** komutumuz mevcut. **JG** komutuna, karşıt ve eşlerine bakacağız. Aşağıya yukarıdaki resmin bulunduğu satırları bırakıyorum. Daha iyi incelemeniz açısından.

```
009A2066  |. 68 60229A00    PUSH Kütüphan.009A2260                   ;  UNICODE "select * from KULLANICI where KADI='"
009A206B  |. 8D55 F8        LEA EDX,DWORD PTR SS:[EBP-8]
009A206E  |. 8B86 C8030000  MOV EAX,DWORD PTR DS:[ESI+3C8]
009A2074  |. E8 C73FBAFF    CALL Kütüphan.00546040
009A2079  |. FF75 F8        PUSH DWORD PTR SS:[EBP-8]
009A207C  |. 68 B8229A00    PUSH Kütüphan.009A22B8
009A2081  |. 68 C8229A00    PUSH Kütüphan.009A22C8                   ;  UNICODE " and SIFRE="
009A2086  |. 68 B8229A00    PUSH Kütüphan.009A22B8
009A208B  |. 8D55 F4        LEA EDX,DWORD PTR SS:[EBP-C]
009A208E  |. 8B86 CC030000  MOV EAX,DWORD PTR DS:[ESI+3CC]
009A2094  |. E8 A73FBAFF    CALL Kütüphan.00546040
009A2099  |. FF75 F4        PUSH DWORD PTR SS:[EBP-C]
009A209C  |. 68 B8229A00    PUSH Kütüphan.009A22B8
009A20A1  |. 8D45 FC        LEA EAX,DWORD PTR SS:[EBP-4]
009A20A4  |. BA 07000000    MOV EDX,7
009A20A9  |. E8 C693A6FF    CALL Kütüphan.0040B474
009A20AE  |. 8B45 FC        MOV EAX,DWORD PTR SS:[EBP-4]
009A20B1  |. 50             PUSH EAX
009A20B2  |. 8BC3           MOV EAX,EBX
009A20B4  |. E8 57AADBFF    CALL Kütüphan.0075CB10
009A20B9  |. 5A             POP EDX
009A20BA  |. 8B08           MOV ECX,DWORD PTR DS:[EAX]
009A20BC  |. FF51 30        CALL DWORD PTR DS:[ECX+30]
009A20BF  |. 8BC3           MOV EAX,EBX
009A20C1  |. E8 6AACCDFF    CALL Kütüphan.0067CD30
009A20C6  |. B2 01          MOV DL,1
009A20C8  |. 8BC3           MOV EAX,EBX
009A20CA  |. 8B08           MOV ECX,DWORD PTR DS:[EAX]
009A20CC  |. FF91 C8010000  CALL DWORD PTR DS:[ECX+1C8]
009A20D2  |. 8BC3           MOV EAX,EBX
009A20D4  |. E8 1FE4CDFF    CALL Kütüphan.006804F8
009A20D9  |. 8BC3           MOV EAX,EBX
009A20DB  |. 8B10           MOV EDX,DWORD PTR DS:[EAX]
009A20DD  |. FF92 94010000  CALL DWORD PTR DS:[EDX+194]
009A20E3  |. 85C0           TEST EAX,EAX
009A20E5     7F 3A          JG SHORT Kütüphan.009A2121
009A20E7  |. 6A 30          PUSH 30
009A20E9  |. 68 E0229A00    PUSH Kütüphan.009A22E0                   ;  UNICODE "Hata!"
009A20EE  |. 68 EC229A00    PUSH Kütüphan.009A22EC
009A20F3  |. 8BC6           MOV EAX,ESI
009A20F5  |. E8 9ED3BAFF    CALL Kütüphan.0054F498
009A20FA  |. 50             PUSH EAX                                 ; |hOwner
009A20FB  |. E8 E44BA7FF    CALL <JMP.&user32.MessageBoxW>           ; \MessageBoxW
009A2100  |. 8B86 C8030000  MOV EAX,DWORD PTR DS:[ESI+3C8]
009A2106  |. 8B10           MOV EDX,DWORD PTR DS:[EAX]
009A2108  |. FF92 34010000  CALL DWORD PTR DS:[EDX+134]
009A210E  |. 8B86 CC030000  MOV EAX,DWORD PTR DS:[ESI+3CC]
009A2114  |. 8B10           MOV EDX,DWORD PTR DS:[EAX]
009A2116  |. FF92 34010000  CALL DWORD PTR DS:[EDX+134]
009A211C  |. E9 F5000000    JMP Kütüphan.009A2216
009A2121  |> 8BC3           MOV EAX,EBX
009A2123  |. 8B10           MOV EDX,DWORD PTR DS:[EAX]
009A2125  |. FF92 94010000  CALL DWORD PTR DS:[EDX+194]
009A212B  |. 85C0           TEST EAX,EAX
```

![](https://i.hizliresim.com/LvrMj0.png)

**Breakpoint** ekliyoruz ki; nerede kaldığımızı unutmayalım. **Breakpointi** ekledik. Dikkatimi tabi ki SQL sorgusu çekti. Demiştik bu makalemizde 2 farklı yöntem kullanacağız. Şimdi SQL injection tarafına kayalım.


### SQL Injection Nedir? ❓️
 Google amca bize bunu söylüyor;
  

> SQL Injection, veri tabanına dayalı uygulamalara saldırmak için kullanılan bir atak tekniğidir; burada saldırgan SQL dili özelliklerinden faydalanarak standart uygulama ekranındaki ilgili alana yeni SQL ifadelerini ekler. 

Benim tabirim ile kısaca; dışarıdan SQL sorgusu kod enjekte etmeye SQL injection denir. Daha iyi tanımı olan aşağıda belirtebilir ve bu makale eşsiz bir hal alabilir bence ❤

Şimdi başlıca login için denemelik exploit tarzında bazı **SQL** tümlecikleri mevcuttur. Bunları SQL injection için kullanabiliriz. Bu doğrultuda denemeler ile **SQL injection** ile **login** ekranını atlatabiliriz. Şimdi bir takım SQL injection için tümleciklerine göz atalım. Genel itibari ile kullanılan bir injection mevcut.

Bir takım injection örnekleri:

`select username,pass from users where username=('$username') and password=('$passwrd') limit 0,1;`

- ') or true--
- ') or ('')=('
- ') or 1--
- ') or ('x')=('

`select username,pass from users where username="$username" and password="$passwrd" limit 0,1;`

- " or true--
- " or ""="
- " or 1--
- " or "x"="

Daha fazlası için [tıklayın.](http://www.securityidiots.com/Web-Pentest/SQL-Injection/bypass-login-using-sql-injection.html)

Popüler olan:
`' OR 1=1 --'`

SQL'i ile test yapalım. 

![](https://i.hizliresim.com/GZVAzr.png)

Yukarıdaki gibi SQL injection işlemimizi gerçekleştiriyoruz ve bypass yapmaya çalışıyoruz.

![](https://i.hizliresim.com/gPa0oO.png)

ve bypass başarılı.

### Peki Bunun Mantığı Nedir? 👨‍💻
Büyük ihtimalle sql sorgusu şu şekilde yazılmış; 
  `"select * from kullanici where kadi="+textbox1.text+" and sifre="+textbox2.text;`
  
Zaten reverse ederken bu **SQL** sorgusunu görmüştük. **TextBox** veya **Delphi**'de ki karşılığı **Edit** nesnesine yazacağımız kod SQL sorgusunda da değişikliğe sebep olacağından bu şekilde **SQL** sorgumuzu ek bir SQL enjekte edip sonucumuzun 1 gelmesini sağlıyoruz. Böylelikle **if** kontrolünde kaydın olduğu anlaşılıyor ve **login** ekranını geçmiş oluyoruz. **Reverse** tarafında ise yapacağımız aslında şu; **minimum** 1 kayıt isteyen kontrolü ters çevirip doğru kayıt haricinde **random** kayıt ile giriş yapabilmeyi sağlamak.

Bunun iki önlemi var:
1. Parametre kullanınız.
2. ', " gibi karakterlerin kullanımını yasaklayınız.

Makalemizin sonunda bu tip durumlar için **Delphi** dilinde nasıl bir önlem alınmalı ona göz atacağız. Şimdi reverse kısmına bakalım.

### Reverse ile ByPass 👨‍💻
**009A20E5** adresinde bulunan kontrolü ele alalım. Bu kontrolü ters çevirerek testimize başlayalım. Büyük ihtimalle geçişimize izin verecektir.

### 80x86 Komut Setinde JG, JLE Komutları 👨‍💻
`JG` ve `JLE `iki karşıt komutlardır. `JG `komutu işaretli sayılar için kullanılan komuttur. `JLE`'de aynı şekilde. Aralarındaki fark birisi büyükse anlamına gelirken(`JG`) diğeri ise(`JLE`) eşit veya düşükse anlamına gelmektedir. JG komutu ile bir değişken, bir sayıdan büyük ise bunu işle denmekte. Biz bunu tersine çevireceğiz. 

```
009A20DD  |. FF92 94010000  CALL DWORD PTR DS:[EDX+194]
009A20E3  |. 85C0           TEST EAX,EAX
009A20E5     7F 3A          JG SHORT Kütüphan.009A2121 ///burada değişiklik yapacağız.
009A20E7  |. 6A 30          PUSH 30
009A20E9  |. 68 E0229A00    PUSH Kütüphan.009A22E0                   ;  UNICODE "Hata!"
009A20EE  |. 68 EC229A00    PUSH Kütüphan.009A22EC

```

Yukarıdaki 6 satırın içinde bulunan komutu ters çevirip devam edelim.

![](https://i.hizliresim.com/p5132q.png)

Başarıyla değişikliği yapmış olduk ve satırımız aşağıdaki hali aldı.

```
009A20DB  |. 8B10           MOV EDX,DWORD PTR DS:[EAX]
009A20DD  |. FF92 94010000  CALL DWORD PTR DS:[EDX+194]
009A20E3  |. 85C0           TEST EAX,EAX
009A20E5     7E 3A          JLE SHORT Kütüphan.009A2121 //değişikliği gerçekleştirdik.
009A20E7  |. 6A 30          PUSH 30
009A20E9  |. 68 E0229A00    PUSH Kütüphan.009A22E0                   ;  UNICODE "Hata!"
```

Tam olarak test etmeden önce biraz daha aşağı inip kontrol ediyorum ne var ne yok diye.

![](https://i.hizliresim.com/JV1JWj.png)

Yukarıdaki resimde görüldüğü üzere iki tane şart mevcut. Bunu hemen değiştirmeden ne olduğunu anlamaya çalışacağım. Aşağısında unicode biçiminde **ID**, **ADSOYAD** gibi tanımlar görüyorum. Buradan veritabanında bulunan alanlar olduğu açık. Peki bunları ne yapıyor diye sorarken kendime **data** klasöründe bulunan **kullanici.txt** dosyasında bilgiler gördüm.

kullanici.txt içeriği:
```
 4
 Halil Han Badem
```

Demek ki kullanıcı bilgilerini bu şekilde bir yere kaydediyor. O zaman buradan bir çekiş söz konusu. Acemi şekilde olmasa değişkene atardı veya **OOP**'ye uygun şekilde bu tip kayıtlar için **property** oluşturup onlara `set` eder gerektiği yerde `get `edip bilgiyi kullanırdı. Tabi dediğim gibi ilk zamanlar uygulaması ve bu şekilde sıkıntıların olması, kıt düşüncenin yaşanması kadar doğal bir şey yok. Eğer kodları incelesek "*ulan böyle kodu compiler nasıl kabul ediyor" deriz herhalde...

Devam edip bir ön tahminde bulunuyorum ve büyük ihtimalle else değişkenin altında bir` if` olduğunu yani çift kontrol yaptığını tahmin ediyorum. O yüzden onu da tersine çevirip **build** ediyorum.

```
009A2125  |. FF92 94010000  CALL DWORD PTR DS:[EDX+194]
009A212B  |. 85C0           TEST EAX,EAX
009A212D     0F8E E3000000  JLE Kütüphan.009A2216 ///değişeceğimiz adres
009A2133  |. B2 01          MOV DL,1
009A2135  |. A1 1C374900    MOV EAX,DWORD PTR DS:[49371C]
```

bu şekilde olan adres aralığı bu şekilde değişiyor.

```
009A2125  |. FF92 94010000  CALL DWORD PTR DS:[EDX+194]
009A212B  |. 85C0           TEST EAX,EAX
009A212D     0F8F E3000000  JG Kütüphan.009A2216 //değişen adres
009A2133  |. B2 01          MOV DL,1
009A2135  |. A1 1C374900    MOV EAX,DWORD PTR DS:[49371C]
```

![](https://i.hizliresim.com/9Yy1WO.png)

Değişikliğimizi yukarıdaki gibi halletik. Şimdi ise build edip sonuca göz atalım. 

![](https://i.hizliresim.com/4p4oQY.png)

test ve test diyerek giriş yapmaya çalışıyorum. Veritabanında ola ki böyle bir kayıt varsa yanlış diyecek eğer yoksa doğru demesi gerekir.

![](https://i.hizliresim.com/JV1JNE.png)

ve sonuç başarılı...

Video formatlı aşağıya ekliyorum. Siteye güvenemedim link kırılırsa es geçin :)
![](https://s5.gifyu.com/images/Untitled-Projectb64a189ae317c793.gif)

### SQL Injection Güvenliği Nasıl Alınır?
Delphi tarafında bu tip önlemler için fonksiyon oluşturup kullanıcının verdiği veriyi kontrol etmek en sağlamıdır diyebiliriz. [Bu makalede](https://yazilimtoplulugu.com/d/401) bulunan XSS korumasına benzer bir fonksiyon yazıp girişleri önleyelim.

```
uses StrUtils;
...

function SQLInjectionProtected(Value: string): Boolean;
const
  blockTags: Array[0..1] of AnsiString=(#39, '"');
var
 I: Integer;
begin
  Result := False;
  for I := 0 to Length(blockTags) - 1 do
    begin
      if ContainsText(Value, blockTags[I]) then
      begin
        Result := True;
      end;
    end;
end;
```

Bu fonksiyon ile test edelim bakalım başka bir uygulamada;

![](https://i.hizliresim.com/GZVA77.png)

Evet sorunsuz bir şekilde tespitini yapabiliyoruz. İkincil bir önlem ise + ifadesi yerine parametre kullanmak. Yani aşağıdaki gibi:

```
 query.Close;
 query.SQL.Clear;
 query.SQL.Text := 'SELECT * FROM KULLANICI WHERE KADI=:KADI AND KSIFRE=:KSIFRE';
 query.ParamByName('KADI').AsString := Edit1.Text; //.netciler için TextBox anlamına gelir.
 query.ParamByName('KSIFRE').AsString := Edit2.Text;
 query.Open;
```

Gibi şeklinde önlemler alındığında temel anlamda güvenlik sağlanmış olur. Tabi yine de %100 güvenlidir diyemeyiz. 
Şimdi ise bizim yazdığımız koda bakalım yani eskiden yazmış olduğum kod, hazır olun...

```
var
 GirisQuery: TUniQuery;
 Kullanici, Settings: TStringList;
begin
 ActiveControl := nil;

 GirisQuery := TUniQuery.Create(nil);
 GirisQuery.Connection := UniConnection1;
 GirisQuery.Close;
 GirisQuery.SQL.Text := 'select * from KULLANICI where KADI='+#39+LabeledEdit1.Text+#39+' and SIFRE='+#39+LabeledEdit2.Text+#39;
 GirisQuery.Open;
 GirisQuery.Active := True;
 GirisQuery.Refresh;

 if GirisQuery.RecordCount <= 0 then
 begin
   MessageBox(handle, 'Kullanıcı adı veya şifrenizde bir hata mevcut, lütfen kontrol ediniz.', 'Hata!', MB_OK + MB_ICONWARNING);
   LabeledEdit1.Clear;
   LabeledEdit2.Clear;
 end
 else
 if GirisQuery.RecordCount > 0 then
 begin
   Kullanici := TStringList.Create;
   Kullanici.Add(IntToStr(GirisQuery.FieldByName('ID').AsInteger));
   Kullanici.Add(GirisQuery.FieldByName('ADSOYAD').AsString);
   Kullanici.SaveToFile('data\kullanici.txt');

   Settings := TStringList.Create;
   Settings.LoadFromFile('Settings.ini');

   KMenu.Show;

   if Settings.Strings[1] = 'StartUp=0' then
   begin
    KLisansSozlesmesi.ShowModal;
    KTasarimSecimi.ShowModal;
   end;

   Settings.Strings[1] := 'StartUp=1';
   Settings.SaveToFile('Settings.ini');
 end;
end;
```

Merak etmeyin bende diyorum; "bu ne olm!??"...

### Son 🏁
 Veee geldik uzun yazımızın sonuna. Umarım anlaşılır ve size katkı sağlayan bir yazı olmuştur. Yazım hatalarını, yanlış bilgi durumlarında çekinmeden kurallar çerçevesinde belirtebilir, tartışabiliriz. Gerektiğinde yanlış yerleri beraber düzeltebiliriz. Merak etmeyin çoğu insanda olan "eleştiriyi kabullenmeme" durumu bende yok. Yanlış olan birşeyin doğrusunu öğrenmek benimde en çok istediğim şeydir. Bu ve bu tip makaleler için lütfen forumumuza üye olun. Böylelikle daha kolay erişebilir dilerseniz **Android** uygulamamız ile forum içerisinde surf yapabilirsiniz. 

Reverse konusunda zamanım oldukça makale hazırlayacağım. Bu konudaki sorularınızı aşağıda belirtebilirsiniz.
Kendinize iyi bakın,
sağlıcakla kalın!
