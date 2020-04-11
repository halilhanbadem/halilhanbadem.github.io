---
layout: post
author: Halil Han Badem
title: Uygulamaya Müdahale Nasıl Yapılır?
---
Merhabalar,

Bu makalemde sizlerle birlikte bir uygulama üzerinde dışarıdan nasıl müdahale edilebileceğini öğreneceğiz. Bu makale serisi çok uzun olacak. Bu konu uzun olmasından dolayı ders niteliğinde aşama aşama makaleler yazmaya özen göstereceğim. Bu makale Türkçe olarak sadece bu forumda bulunmaktadır. Başka forumlara izinsiz kullanılması durumunda konuyu açan üye hakkında ağır yaptırımlar uygulanır. 

Detaylı bilgi için [Kurallar](https://yazilimtoplulugu.com/p/1-kurallar) sayfasına göz atabilirsiniz.

### Araçlarımız

Debugging konusunda birçok araç vardır. Benim kullandığım başlıca uygulamalar x32dbg ve Ollydbg. Bu uygulamaları aşağıdaki linkten indirebilirsiniz.

[x32dbg](https://x64dbg.com/#start) 

> Bu uygulama x64dbg diye de geçmektedir. 32/64 bit olarak kendi içinde ayrılmıştır. Türkçe desteği mevcuttur.

[Ollydbg](http://www.ollydbg.de/download.htm)

> Ben bu uygulamayı kullanacağım.

### Ollydbg Aracımızı Tanıyalım... Kısaca 😁 

![](https://i.hizliresim.com/odZmR9.png)

Yukarıdaki resimde görüldüğü üzere iki üst menümüz mevcut ve 4 ayrı penceremiz mevcut. En yukarıda ki menü "File, View, Debug" gibi standart menüler. Bu menüler içerisinde birçok işlem yapma imkanınız var fakat bunu anlatmak ayrı bir makale gerektirir. Bu konu içinde ayrıca bir makalem olacaktır. 

> Executable yapısı modüller tarzda Windows modülleri ile işlem yapar. Örnek vermek gerekirsem bir uygulama kernel32.dll, kernelbase.dll, sechost.dll... gibi birçok DLL dosyasına erişim sağlayarak işlemleri gerçekleştirir. 

Şimdi File -> Open diyerek bir örnek executable dosyamızı açalım. 

![](https://i.hizliresim.com/odZmb7.png)

Bu resmi sayılara göre anlatacağım. Başlayalım. 

1. Soldan sağa olmak üzere; dosya açma, en son debugging etmiş olduğunu dosyayı tekrar debugging etmek ve işleminizi sonlandırma düğmesi bulunmaktadır. 
2. Soldan sağa olmak üzere; uygulamayı bütünüyle debug eder, sadece geçerli kanaldan uygulamayı debug eder, debug işlemini duraklatır.
3. Trace into/over, step into/over gibi işlemler "iz sürer" diye anlatmam uygun olmaz. Yardımcı bir makale ile sizlerin bu konuda bilgi sahibi olmasını sağlayabilirim çünkü konunun kısa ve öz olması için uğraşıyorum. Bahsi geçen makale [Run trace tutorial](http://www.ollydbg.de/Tut_rtr.htm)
4. L, E, M, T, C; Log penceresini açar, uygulama modül listesini, bellek haritasını, kanal listesini, CPU penceresini açar. 
5. Diğer sarı alanlar breakpointler için. Breakpoint ile geçmişte haşır neşir olan az çok neler olabileceğini tahmin edebiliyordur. Şu anda işimize 1, 2, 4, 6. maddeler yarayacak
6. Bu alanda assembly dili ile uygulama içerisini listelemekte. CPU üzerinde ki işlemleri assembly dilinde bize sanal adresler ile birlikte sunmakta. 
7. Bu kısımda ise ASCII desteği ile bellekte bulunan(uygulamaya ait) adresleri HEX şeklinde göstermekte.
8. FPU yani Floating-point birimi matematiksel işlemleri yapmak için işlemciye entegre olan bir tür çiptir. Bu kısımda ise bu matematiksel çipinin kaydında bulunan assembly değerleri bulunur. Dolayısıyla kısaca CPU'da bulunan kayıtlar diyebiliriz. 
9. Burada ise 7 numaradan farklı olarak "stack" yani uygulamanın yığınında bulunan bilgiler yer alır. Bu bilgiler boşaltıp bir dosyaya aktarımını yaparak inceleyebilirsiniz.

> Bir uygulama yığını, belirli bir görevi yerine getirmeye yardımcı olan bir takım veya bir dizi uygulama programıdır. Bu uygulamalar birbirine yakından bağlıdır ve veriler en az adımlarla aralarında ihraç edilebilir veya alınabilir. Birçok ofis uygulaması, tek bir uygulama yığında kelime işlem, elektronik tablolar, veritabanları ve e-posta yardımcı programlarını içerir.

Şimdi çok kısa da olsa özet geçmiş olduk. Çalışan bir uygulamaya müdahale etmek için bu araçlar çok işimize yarıyor. Tabi bunu assembly kısmı var biz bu yazıda assembly dokunmayacağız. Yabancılık çekmemeniz açısında sizlere kısaca assembly syntax'ını temel mantığını anlatmak isterim.

```
mov eax, 2
add eax, 2
add eax, 5
ret
```

`eax` anahtarına 2 yazıyoruz. Sonrasında` add eax, 2` ile `eax` anahtarına 2 ekleyip tekrar 5 ekliyoruz. Register yani anahtarın son değerini `ret` komutu ile geri dönderiyoruz.

> Assembly konusunda kendimi daha çok geliştirtikçe ve makale yazma düzeyine gelince makaleler yazacağım.

### Bir Uygulamaya Müdahale Edelim

Hemen Delphi ile bir uygulama oluşturuyoruz. Benim elimde en kolay ulaşabileceğim IDE Delphi olduğu için Delphi üzerinden örnek veriyorum. Bir exe olsun kafi yani :)

Delphi uygulamasında bir adet buton olacak ve bu butona tıklayınca mesaj olarak "yazilimtopluluguforum" diye gösterim sağlayacak. Biz ise müdahale ederek bu mesajı değiştireceğiz. Uygulamamızı yazalım;

```
unit Unit1;

interface

uses
  Winapi.Windows, Winapi.Messages, System.SysUtils, System.Variants, System.Classes, Vcl.Graphics,
  Vcl.Controls, Vcl.Forms, Vcl.Dialogs, Vcl.StdCtrls;

type
  TForm1 = class(TForm)
    Button1: TButton;
    procedure Button1Click(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.dfm}

procedure TForm1.Button1Click(Sender: TObject);
begin
 ShowMessage('yazilimtopluluguforum');
end;

end.

```

![](https://i.hizliresim.com/P7odk5.png)

Bu şekilde sonuç alıyoruz. Uygulamayı aracımıza yüklüyor ve işlemler sırasıyla yapıyorum. 

### Müdahale Zamanı!

Uygulamayı File -> Open ile açtım ve karşıma assembly, ASCII, HEX, sanal adreslerden oluşan pencere açıldı. Hemen yukarıda araç tanıtımını yaparken olduğu gibi. İlk aşamada Alt+M yaparak bellekte olan değerlerin olduğu pencereyi istiyorum ve açıyorum.

![](https://i.hizliresim.com/gPAgoO.png)

Sonrasında ise sağ tık yapıyorum. "**Search**" diyerek arama penceremi açıyorum. 

![](https://i.hizliresim.com/Ydly42.png)

Burada ASCII, UNICODE ve HEX tarzında aramalar gerçekleştirebilirsiniz. Biz **yazilimtopluluguforum** adında bir arama yapacağız.

![](https://i.hizliresim.com/Lv4MZ0.png)

Aramamızı gerçekleştirdik. Tabi aramayı gerçekleştirmeden önce kırmızı çizgi ile çizdiğim ve ok ile gösterdiğim bölümde aslında .text, .itext alanlarını görüyorum. Burada text tabanlı verilerin olduğunun tahminini yapabiliriz. Buradan da yola çıkabiliriz veya daha kolay yol olan arama özelliğinide kullanabiliriz. Arama özelliğini kullanıp "Search" diyerek başlatıyorum ve hızlı bir aramanın ardından tahmin ettiğimiz gibi .itext bölümünde çıktı. 

![](https://i.hizliresim.com/7BbJql.png) 

Yan formda açıkca görüyoruz ki **y.a.z.i.l.i.m.t.o.p.l.u.l.u.g.u.f.o.r.u.m** yazmakta. Arama yaptığımı için otomatik seçili ama siz düzenlemek istediğiniz yeri fare ile seçip sonrasında sağ tık yapıp aşağıda ki adımları izleyerek düzenleme yapabilirsiniz.

![](https://i.hizliresim.com/AOqV1z.png)

Bu aşamaya geldikten sonra önümüze bir adet form gelecek. Bu form üzerinde ASCII, HEX veya Unicode biçiminde yine değişiklik yapabiliyoruz. Yapalım öyleyse :)

![](https://i.hizliresim.com/86bOzA.png)

Değişiklik yapıyoruz

![](https://i.hizliresim.com/r0V45V.png)

Ok dedikten sonra yukarıda 2 numarada tanıtımını yaptığım **Run** tuşuna basıyoruz. Sonuç olarak ise uygulamamız önümüze geliyor ve düğmeye basıyoruz.

![](https://i.hizliresim.com/6DkBa3.png)

Başarılı! Bu şekilde uygulama çalışır vaziyette iken düzenlemeler/müdahaleler yaparak birçok değişiklik yapabiliriz. Bu kısımda söylemek istediğim birkaç önemli durum var. Code signing yani kod imzalama işleminin ne kadar önemli olduğunu. Bu şekilde uygulamaya enjekte edilecek olan bir zararlı yazılımın hem uygulamanın firmasına hemde kullanıcıya ne denli zarar vereceği meçhul ama bilinmelidir ki büyük firmaların uygulamalarını indirdiğinizde kodu imzalı değilse suçlamayı hiçbir şekilde firmaya yapamazsınız. Suçu kendinizde aramanız gerekmektedir. Çok zorunlu olmadığı sürece cracklı uygulama kullanmayalım diyerek ve kendi bilgisayarımda işletim sistemi hariç tüm uygulamaların crackli olduğunu hatırlatarak ilk bölümü bitirmiş oluyorum. 

Umarım yararlı bir makale olmuştur,
Okuduğunuz için teşekkür ederim,
İyi çalışmalar :D
