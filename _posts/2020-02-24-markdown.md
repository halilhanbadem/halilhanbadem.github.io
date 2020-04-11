---
layout: post
author: Halil Han BADEM
title: Uygulama Analizi ve Saldırı
---

Merhabalar,

Bu makalemizde sizler ile beraber bir uygulamanın analizini yapıp sonrasında gerekli işlemleri gerçekleştirip bu işlemler sonucunda kendi keygenizimizi nasıl yapacağımızı anlatacağım. Yavaştan başlayalım.

### Uygulama Hakkında

Uygulama bir bakım aracı olan Glary Ultilites adlı uygulama. İnternette çokça pro anahtaları mevcut. Bu anahtarlar çoğu çalışır durumda. Peki hepsi çalışıyor ise bu durumdan nasıl bir sonuç çıkarmalıyız. İlk olarak lisans kontrol sistemi konusunda biraz konuşalım.

![](https://i.hizliresim.com/Lv2ZBb.png)

Uygulamaya ilk bakışımızda birçok araca sahip ve başarılı sayılabilecek bir ana araç. Peki lisans kontrolü nasıl gerçekleşiyor. İlk aşamada internette bulduğumuz bir anahtarı alıp denemesini yapıyoruz. 

> Bu işlem internet yokken yapılmaktadır. Amacımız internet üzerinden herhangi bir kontrolün olup olmadığı.

![](https://i.hizliresim.com/r0j3BB.png)

Örnek olarak vermiş oldukları anahtarı deneyince **internet olmaksızın** yine de bir kontrol gerçekleştirip yanlış olduğunu belirtiyor. Birde çalışan bir anahtar bulup çalışıp çalışmadığını kontrol edelim.

![](https://i.hizliresim.com/lQLdYl.png)

Kontrol sonucunda internetsiz ortamda bile bu anahtarı kabul ediyor. Burada çıkaracağımız sonuç uygulama içerisinde web servis düzeneği yerine bir algoritma kullanılmış. Runtime olarak kendi doğrulama mekanizması ile aktifleştiriyor. Verilen anahtarlar ise kafadan değil yapmış oldukları ve bu mekanizmaya uygun anahtar çıkaran bir küçük araç aslında. Peki biraz daha işin içine girelim...

Aslında bu bir şifre :) yani bahsettiğimiz mekanizma aslında bu anahtarı alınca çözüp kullanıcının kaç aylık olduğunu ve pro versiyon mu yoksa daha farklı bir versiyona sahip olduğunu çözüyor. Bu bir felaket! Yine uyarıyorum asla ama asla lisans kontrol sisteminizi uygulama içine eklemeyiniz. Uzak sunucu olunca da çok agresif bir doğrulama mekanizması oluşturarak işlemlerinizi yapmayı tercih edin. Yine de uygulamanızın sunucu adres dosyası veya -obfuscator kullanmadıysanız saldırgan için daha kolay olur- bilgeleri belirttiğiniz değişkeninize müdahale ederek veya dosyaya müdahale ederek manipüle edilebiliyor. Bunun içinde önlem almak gerekir. Çıtkırıldım ama bir o kadarda sağlam bir doğrulama sistemi oluşturmak faydanıza olacaktır. Zaten her saldırgan sizin yazdığınız her uygulamaya saldırmaz. Para etmesi, popüler olması saldırması için ideal nedenler... günümüzde uygulamaların kırılmama hale getirmek imkansız. Yine inandığımız güvenliğin olmadığını sadece saldırganın işini zorlaştırma sanatı bu yaptıklarımız. Kafaya koyan her türlü sistemi kırar, biz elimizden gelen tüm önlemleri almış oluyoruz. 

Devam edelim...

Lisans localden kontrol ediliyor ve çalışan bir keygen var elimizde. Peki bu keygen nerede saklanıyor. Saklanması lazım çünkü sürekli kontrol var. İnternet yoksa uzak sunucu kontrolü yok demektir. Bu cepte! bakacağımız yerler:

1. Uygulamanın kurulu olduğu dizinde ki dosyalar.
2. AppData klasörünü ayrıntılı arama. **Uygulama dizinini**.
3. Reg defterine göz atmak.

Bu durumlar çoğaltılabilir ama biz direkt bu 3 maddeyi referans alalım. Kontrollerimi gerçekleştirdiğimde uygulama dizininde bu tür dosyanın saklandığını tespit edemedim. Açılmayan dosyaları isimlendirmesinden tahmin ederek; açılanları ise sıkı bir araştırma ile halletmiş oldum. 2. madde'de durum aynı kaldı 3.madde. Hemen bir araştırma yapıyoruz.

![](https://i.hizliresim.com/NL3pqO.png)

Local bilgiler **HKEY_LOCAL_MACHINE** içerisinde bulunur. Yazılım firmaları çoğunlukla ilgili klasör yani **SOFTWARE** klasörünü tercih eder. Böylelikle bilgileri oradan saklarlar. Biz **GlarySoft** olarak geçerli olan klasörü bulup içerisini kontrol ediyoruz.

İlk aşamada bir klasör sonucu bulamadık WOW6432Node klasörüne de göz atıyoruz. 64 destekli uygulamaların kullandığı klasör diyebiliriz. 

> WOW6432Node klasörü Microsoft'un Windows üzerinde 64-bitlik ve 32-bitlik anahtarları bölüştürmesinden dolayı oluşan klasörüdür. 64-bitlik anahtarlar bu klasörde saklanır.

![](https://i.hizliresim.com/anjLMQ.png)

Bulduk! Her config bilgisi burada mevcut. UpdateUrl parametresinden tutunda RegName, RegCode gibi parametrelere kadar mevcut. Az önce lisanslama yapmıştık onun bilgileri bile duruyor. Şimdi herşey elimizde. Tek yapmamız gereken bulduğumuz anahtar ile işlemleri gerçekleştirmek. 

### Bu Duruma Uygun **Keygen** Yapalım!

Ne bir tersine mühendislik ne de bir assembly kodu ile bypass etmek. Hiçbirine gerek duymadan bir keygen yapma şansı yakaladık. Tabi bu durum gerçek cracklar için çerezlik durum. Mesele ters mühendislik ile assembly - bypass ederek recompile ederek tam bir keygen oluşturmak. İlerde bu konuda makaleler gelecektir. O yüzden forumu takip etmeyi bırakmayın!

> Bu proje Delphi dili ile yazılmıştır.

**untKeygen1.pas**

```
unit untKeygen1;

interface

uses
  Winapi.Windows, Winapi.Messages, System.SysUtils, System.Variants, System.Classes, Vcl.Graphics,
  Vcl.Controls, Vcl.Forms, Vcl.Dialogs, Vcl.ExtCtrls,
  Vcl.StdCtrls, ShellApi, Registry, dxGDIPlusClasses;

type
  TfrmKeygen = class(TForm)
    header_logo: TImage;
    edtLicenseName: TEdit;
    Label1: TLabel;
    btnPatch: TButton;
    btnAbout: TButton;
    procedure btnAboutClick(Sender: TObject);
    procedure btnPatchClick(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

  const
   Key = '2788-6167-9587-68';

var
  frmKeygen: TfrmKeygen;

implementation

{$R *.dfm}

procedure TfrmKeygen.btnPatchClick(Sender: TObject);
var
 Key_Save: TRegistry;
begin
 try
  Key_Save := TRegistry.Create(KEY_WRITE);
  with Key_Save do
  begin
   RootKey := HKEY_LOCAL_MACHINE;
   if KeyExists('SOFTWARE\WOW6432Node\GlarySoft\Glary Utilities 5') = True then
   begin
    OpenKey('SOFTWARE\WOW6432Node\GlarySoft\Glary Utilities 5', False);
    WriteString('RegName', edtLicenseName.Text);
    WriteString('RegCode', Key);
    CloseKey;
    MessageBox(handle, 'Patch OK!', 'OK', MB_OK + MB_ICONINFORMATION);
   end
   else
   begin
     MessageBox(handle, 'Make sure the Glary Ultilities application is installed!', 'Error', MB_OK + MB_ICONERROR);
     exit;
   end;
  end;   
 finally
   FreeAndNil(Key_Save);
 end;
end;

procedure TfrmKeygen.btnAboutClick(Sender: TObject);
begin
 ShellExecute(handle, 'open', 'https://halilhanbadem.com', nil, nil, SW_SHOWMAXIMIZED);
 MessageBox(handle, 'Developer by: Halil Han Badem', 'Developer', MB_OK + MB_ICONINFORMATION);
end;

end.
```

### Sonuç

Aslında bunlardan hepsinin geliştirici firmanın haberi var. Hepsi bilerek yapılmış ve bilerek izin veriliyor. **Pazarlama stratejisi** diyerek bu makalemizin sonuna geliyoruz. 

İyi çalışmalar! 
