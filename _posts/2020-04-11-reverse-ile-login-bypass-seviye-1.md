---
layout: post
author: Halil Han Badem
title: Reverse ile Login ByPass - Seviye 1
---

### Giriş 🗣️ 

Selamlar,

Bu yazı serimizde kolaydan zora doğru masaüstü uygulamaların **login** ekranlarını nasıl atlayacağımızı anlatmaya çalışacağım. Bu bilgiler eşliğinde bir uygulamaya koşul ile lisans sorgusu veya kullanıcı bilgisi isteyen bir ekran karşısında neler yapılabileceğini öğrenmeye çalışacağız. Uygulamalarda çok çeşit doğrulama kod algoritması vardır. Bu makale üzerinde anlatacağım uygulama aslında bir nevi **crackme** olarak adlandırılan kırmak için yazdığım bir uygulama. Basitçe if ile kontrol sağlanıyor. Kullanıcı adı ve şifre sabit. Aslında decompile edilse bile yine de kullanıcı adı ve şifreye ulaşılabilir. Geliştirici **aspack** veya **upx** ile pack etmiş ise belki biraz dolanırız veya **obfuscator** metotlarından birini kullanmış ise yine biraz uğraşırdık ama **1.seviye** anlatım olduğundan **en kolay** şekilde anlatmaya çalışacağım. Seviye 2,3,4 diye devam edecek yazı dizimiz de işleri zorlaştırarak bazı konuları netleştirmeye çalışacağım.


### Kullanılan Araçlar 🛠️  

Kullandığımız debug aracı **OllyDbg**. OllyDbg uygulaması ile uygulamamızın login kontrolü devre dışı bırakmaya çalışacağız.


### Adım 1 - Uygulamayı İzleyelim 🙈  

![](https://i.hizliresim.com/9YWVVN.png)

Uygulamamız yukarıda gösterildiği şekilde bir tasarıma sahip. Amacımız ise doğru bilgileri girdiğimizi teyit ettiren mesajı yakalamak. Standart bir şekilde birkaç test yapıyoruz. Kullanıcı adı olarak **admin**, şifre olarak da **admin** şeklinde bir test gerçekleştiriyorum.

![](https://i.hizliresim.com/9YWVEZ.png)

Bu kısımda zaten yanlış olduğunu belirten mesaj mevcut. Mesaj yapısından hangi adreste bu mesajın çıktığını tespit edip, gerekli **breakpointleri** koyarak **ilgili koşulu ters** çevirerek bu durumu **bypass** etmeye çalışacağız.


### Adım 2 - OllyDbg ile İzleyelim 🔍️ 

Uygulamamızı ilgili tool ile açıyoruz. OllyDbg hakkında temel bilgilere [şu makaleden](https://yazilimtoplulugu.com/d/65) ulaşabilirsiniz. Şimdi ilk olarak ilgili adrese ulaşmanın bir yolu belirli bir **string** değerini aratıp o adrese ulaşmaktır. Bunu aşağıdaki adımlar ile gerçekleştiriyoruz:

1) Bu aşamada tüm referans **disassembly** olmuş text tabanlı verileri listeliyoruz.  
![](https://i.hizliresim.com/6DpZP9.png)

2) Sonrasında ise içerisinde arama gerçekleştirmek için arama formunu açıyoruz.
![](https://i.hizliresim.com/EOWV8B.png)

3) Mesaj yapısını referans alacağımızı baz alarak "Bilgiler yanlış..." gibi bir mesaj karşılıyordu. Türkçe karakterlerin ASCII veya farklı bir format ile yazıldığını baz alarak İngilizce'ye uyumlu olan "Bilgiler" kelimesi ile arama gerçekleştiriyorum.
![](https://i.hizliresim.com/nb9Ar5.png)

4) Evet, ilgili texti bulduk. Çift tıklayarak ilgili adrese gidiyoruz.
![](https://i.hizliresim.com/nb9Aj5.png)

5) İlgili adrese geldikten sonra incelemelerde bulunacağız. Bunun için işlemin başladığı kısıma breakpoint koyarak uygulamamızı izleyeceğiz.
![](https://i.hizliresim.com/nb9ARR.png)

### Assembly Bilgilendirmesi ℹ️ 

Bizim planımız if koşulunu diğer adıyla şartlı dallandırmayı ters çevirerek doğru bilgi haricinde her türlü kullanıcı adı ve şifreyi doğru algılamasını sağlamaktı. Biraz düşünelim... Standart bir yazılımda kullanıcı adı ve şifre ikili koşul ile kodlanır. Yani örnek verecek olursam;

**C#**
```
if (a == a & b==b) 
{

}
```

**Delphi** 
```
if (a = a) AND (b = b) then
begin

end;
```

**PHP**
```
if (a == a && b==b)
{

}
```

Tek bir koşul olarak gözükse de iki farklı koşul mevcut. Assembly de koşulu belirten komutlar `JNZ`, `JNE`, `JE` gibi komutlardır. Bunları tek tek anlatacağım şimdi(**sadece eşittir ve eşittir değil komutları için**);

`JE` komutu eşit koşullar için kullanılır. Bu komutun karşıt komutu ise `JNE` komutudur. `JZ` komutu ile aynı işleve sahiptirler.
`JNE` komutu ise eşit değildir koşulu için kullanılır. Bu komutun karşıt komutu yukarıda belirttiğim `JE` komutudur. `JNZ` ile aynı işleve sahiptir.


### Breakpoint ile İzleme 🔍️

Bu aşamada `PUSH `komutunun olduğu yerden başlatarak biraz baştan almayı denedim.
![](https://i.hizliresim.com/9YWVbQ.png)

Breakpoint'lerden aldığım sonuçları kod şeklinde yazacağım:

```
005EB604  /. 55             PUSH EBP
005EB605  |. 8BEC           MOV EBP,ESP
005EB607  |. 33C9           XOR ECX,ECX
005EB609  |. 51             PUSH ECX
005EB60A  |. 51             PUSH ECX
005EB60B  |. 51             PUSH ECX
005EB60C  |. 51             PUSH ECX
005EB60D  |. 51             PUSH ECX
005EB60E  |. 51             PUSH ECX
005EB60F  |. 51             PUSH ECX
005EB610  |. 51             PUSH ECX
005EB611  |. 53             PUSH EBX
005EB612  |. 8BD8           MOV EBX,EAX //EAX, EBX gibi registerlar ile boşluk hesaplaması ve daha sonra kontrolü yapılıyor. Bu komuttan **005EB63F** adresinde ki koşula kadar iki textbox'ın içerisinde ki textleri kontrol ederek veri girişi olup olmadığı kontrol ediliyor.
005EB614  |. 33C0           XOR EAX,EAX
005EB616  |. 55             PUSH EBP
005EB617  |. 68 33B75E00    PUSH Project1.005EB733
005EB61C  |. 64:FF30        PUSH DWORD PTR FS:[EAX]
005EB61F  |. 64:8920        MOV DWORD PTR FS:[EAX],ESP
005EB622  |. 8D55 F8        LEA EDX,DWORD PTR SS:[EBP-8]
005EB625  |. 8B83 D4030000  MOV EAX,DWORD PTR DS:[EBX+3D4]
005EB62B  |. E8 DC4DF4FF    CALL Project1.0053040C
005EB630  |. 8B45 F8        MOV EAX,DWORD PTR SS:[EBP-8]
005EB633  |. 8D55 FC        LEA EDX,DWORD PTR SS:[EBP-4]
005EB636  |. E8 C582E3FF    CALL Project1.00423900
005EB63B  |. 837D FC 00     CMP DWORD PTR SS:[EBP-4],0
005EB63F     74 1F          JE SHORT Project1.005EB660 // JE komutu ile eğer data boş ise gerekli komutları uygulaması için **005EB660** adresi çağırılıyor.
005EB641     8D55 F0        LEA EDX,DWORD PTR SS:[EBP-10]
005EB644     8B83 D8030000  MOV EAX,DWORD PTR DS:[EBX+3D8]
005EB64A  |. E8 BD4DF4FF    CALL Project1.0053040C
005EB64F  |. 8B45 F0        MOV EAX,DWORD PTR SS:[EBP-10]
005EB652  |. 8D55 F4        LEA EDX,DWORD PTR SS:[EBP-C] //girdiğimiz kullanıcı adını alıyor.
005EB655  |. E8 A682E3FF    CALL Project1.00423900
005EB65A  |. 837D F4 00     CMP DWORD PTR SS:[EBP-C],0
005EB65E     75 12          JNZ SHORT Project1.005EB672 //JNZ ile doğru olup olmadığı kontrol ediliyor.
005EB660  |> 83C9 FF        OR ECX,FFFFFFFF
005EB663  |. 83CA FF        OR EDX,FFFFFFFF
005EB666  |. B8 4CB75E00    MOV EAX,Project1.005EB74C
005EB66B  |. E8 5C1AF7FF    CALL Project1.0055D0CC
005EB670  |. EB 76          JMP SHORT Project1.005EB6E8
005EB672  |> 8D55 EC        LEA EDX,DWORD PTR SS:[EBP-14]
005EB675  |. 8B83 D4030000  MOV EAX,DWORD PTR DS:[EBX+3D4]
005EB67B  |. E8 8C4DF4FF    CALL Project1.0053040C
005EB680  |. 8B45 EC        MOV EAX,DWORD PTR SS:[EBP-14] //yazmış olduğumuz şifre EAX registerına aktarılıyor
005EB683  |. 50             PUSH EAX /// EAX PUSH ile yığın hafızaya aktarılıyor.
005EB684  |. 8D55 E8        LEA EDX,DWORD PTR SS:[EBP-18] ///olması gereken şifre oluşturuluyor.
005EB687  |. B8 F4B55E00    MOV EAX,Project1.005EB5F4
005EB68C  |. E8 133EE2FF    CALL Project1.0040F4A4
005EB691  |. 8B55 E8        MOV EDX,DWORD PTR SS:[EBP-18]
005EB694  |. 58             POP EAX ///PUSH hafızaya aktarırken POP ise hafızadan  olması gereken şifreyi alıyor.
005EB695  |. E8 5EF4E1FF    CALL Project1.0040AAF8
005EB69A     75 3C          JNZ SHORT Project1.005EB6D8 /// koşul ile kontrol ediliyor...Bu kontrol ile kontrol yani kullanıcı adı kontrolü.
005EB69C  |. 8D55 E4        LEA EDX,DWORD PTR SS:[EBP-1C] //burada ise şifreler tekrardan set edilerek ikinci koşul yani AND(&)'den sonra gelen şifre kontrolünü kapsıyor.
005EB69F  |. 8B83 D8030000  MOV EAX,DWORD PTR DS:[EBX+3D8]
005EB6A5  |. E8 624DF4FF    CALL Project1.0053040C
005EB6AA  |. 8B45 E4        MOV EAX,DWORD PTR SS:[EBP-1C]
005EB6AD  |. 50             PUSH EAX
005EB6AE  |. 8D55 E0        LEA EDX,DWORD PTR SS:[EBP-20]
005EB6B1     B8 FCB55E00    MOV EAX,Project1.005EB5FC
005EB6B6     E8 E93DE2FF    CALL Project1.0040F4A4
005EB6BB  |. 8B55 E0        MOV EDX,DWORD PTR SS:[EBP-20]
005EB6BE     58             POP EAX
005EB6BF  |. E8 34F4E1FF    CALL Project1.0040AAF8
005EB6C4     75 12          JNZ SHORT Project1.005EB6D8 //İkinci koşulda burada kontrol ediliyor. Sonrası zaten mesajlar gösteriliyor. Şimdi kod modundan dönüp normal moda dönüyoruz.
005EB6C6  |. 83C9 FF        OR ECX,FFFFFFFF
005EB6C9  |. 83CA FF        OR EDX,FFFFFFFF
005EB6CC  |. B8 98B75E00    MOV EAX,Project1.005EB798                ;  UNICODE "Bilgiler do"
005EB6D1  |. E8 F619F7FF    CALL Project1.0055D0CC
005EB6D6  |. EB 10          JMP SHORT Project1.005EB6E8
005EB6D8  |> 83C9 FF        OR ECX,FFFFFFFF
005EB6DB  |. 83CA FF        OR EDX,FFFFFFFF
005EB6DE  |. B8 F8B75E00    MOV EAX,Project1.005EB7F8                ;  UNICODE "Bilgiler yanl"
```

Şimdi yukarıda incelemiş olduğumuz bellek aralığı var. Biz iki koşulu tespit ettik. AND ile kontrol edildiği için iki koşul mevcut. Biri yanlış olunca diğerine uğramadan zaten "gardaşım yanlış nereye" diye söyleniyor uygulama. Biz ilk koşulu yani 005EB69A adresinde ki koşulu değiştirsek bile şifrede takılacağı için bizim hem 005EB69A adresindeki hem de 005EB6C4 adresindeki koşuluda terse çevirmemiz gerekecek. "Ya hak!" diye zehirli okumuzu fırlatıyoruz.

```
005EB65E     75 12          JNZ SHORT Project1.005EB672
005EB69A     75 3C          JNZ SHORT Project1.005EB6D8
```

Normalde bu iki koşul alt alta değil ama yukarıdaki bölümden kestim. Adreslerden zaten bulabilirsiniz. Şimdi biz bu iki komutu tersi yaparak tüm kullanıcı adlarını kabul etmesini sağlayacağız. Hemen tersi komutları yukarıda anlattım gerçekleştiriyoruz.


```
005EB65E     75 12          JE SHORT Project1.005EB672
005EB69A     75 3C          JE SHORT Project1.005EB6D8
```

Değişimi gerçekleştirdik. Şimdi uygulamamızı derleyip test edelim. Başta admin-admin bilgileri ile giriş yapmak istedik kabul etmedi. Şimdi değil admin-admin istersen osas'ın ismini yazın yine size izin verecektir. 

![](https://i.hizliresim.com/2OW2pE.png)

Kabul etti. Güzel bir durum. Bunu derleyip zaten artık bypass şekliyle kullanabilirsiniz. 

### Hatalı Kodlar ☹️ 

Peki uygulamada nasıl kodlar mevcuttu. Nasıl bir yanlış yol izlenmişti? 

Delphi
```
unit Unit1;

interface

uses
  Winapi.Windows, Winapi.Messages, System.SysUtils, System.Variants, System.Classes, Vcl.Graphics,
  Vcl.Controls, Vcl.Forms, Vcl.Dialogs, Vcl.StdCtrls;

type
  TForm1 = class(TForm)
    Button1: TButton;
    kullaniciAdi: TEdit;
    kullaniciSifre: TEdit;
    Label1: TLabel;
    Label2: TLabel;
    procedure Button1Click(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

  resourcestring
   kullaniciAdi_Text = 'test';
   kullaniciSifre_Text = 'sifre';

var
  Form1: TForm1;

implementation

{$R *.dfm}

procedure TForm1.Button1Click(Sender: TObject);
begin
 if (Trim(kullaniciAdi.Text) = '') OR (Trim(kullaniciSifre.Text) = '') then
 begin
   ShowMessage('Kullanıcı adı veya şifre boş!!');
   exit;
 end;

 if (kullaniciAdi.Text = kullaniciAdi_Text) AND (kullaniciSifre.Text = kullaniciSifre_Text) then
 begin
   ShowMessage('Bilgiler doğru! Giriş işlemi başarılı...');
 end else
 begin
   ShowMessage('Bilgiler yanlış! Lütfen tekrar deneyiniz...');
 end;
end;

end.

```

Şimdi arkadaşlar böyle kod yazılmaz. Yani bu kodlama diyor ki; 

> Kardeşim senin reverse yapmana gerek yok, ben zaten kendi kendimi sana reverse ettiririm.

### Kıssadan Hisse ⌨️

1. Kolay düz mantık koşullardan kaçının.
2. Uygulamanızın bilgilerini uygulamanızda değil uzak sunucuda barındırın.
3. Şifrelerinizi md5 gibi doğrulama algoritmaları ile koruyun.
4. Belirli kontroller ekleyin. Bu kontroller anlık olarak değişken tarzında olmalı.
5. Kodlarınızı sıkıştırın. Antivirüsler için kötü bir durum olabiliyor ama güvenlik için bir adım belki.
6. Obfuscator algoritmalarınızı oluşturun. Kendinize ait şifrelemeler ve koşul algoritmaları ile yaygın yöntemlerden kaçının.


### Son 🖥️ 

Yazımızın sonuna geldik fakat söylemeden geçemeyeceğim bir durum var. Uygulamaların güvenliğini sağlamak zordur. Decompile, reverse, sniff gibi yöntemler ile gerek uygulama tabanlı gerekse sunucu tabanlı her türlü veri akışınız izlenip bypass edilebilir. Kendinize ait algoritmalar kullanmayı alışkınlık haline getirin. Tembellik yapmayınız. 

> Jilet Ahmet sevdiğimiz bir abimizdi. Janti adamdı, adabı giyinmeyi çok iyi bilirdi. mesela ben bilmem.
Çok gülerdi, ben gülmem.

Uygulamalarınıza crackerlar tarafından bu şekilde racon kesilmesini istemiyorsanız elinizden geldikçe kendinize ait algoritmalar oluşturun 😂  

Sağlıcakla kalın 🤟 
