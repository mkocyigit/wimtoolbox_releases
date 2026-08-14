# WIMTOOLBOX

**Windows kurulum image'ları (WIM / ESD / SWM) ve ISO'ları için tek pencerelik araç kutusu.**

ISO'yu aç, içindeki `install.wim` ve `boot.wim`'i **mount etmeden** düzenle, kayıt defteri
dosyalarına dokun, ESD'ye çevir, yeni ISO olarak paketle ya da doğrudan önyüklenebilir
USB'ye yaz — hepsi aynı programda, DISM/`imagex`/`oscdimg` komut satırına düşmeden.

<p>
  <img alt="Platform" src="https://img.shields.io/badge/platform-Windows%2010%20%2F%2011%20x64-0078D6">
  <img alt=".NET" src="https://img.shields.io/badge/.NET-10.0%20Windows%20Forms-512BD4">
  <img alt="Dil" src="https://img.shields.io/badge/dil-VB.NET-004880">
  <img alt="Motor" src="https://img.shields.io/badge/motor-wimlib-success">
  <img alt="Arayüz" src="https://img.shields.io/badge/arayüz-Türkçe%20%2F%20English-informational">
  <img alt="Sürüm" src="https://img.shields.io/badge/sürüm-1.0.0-blue">
</p>

> TNCTR üyeleri için Claude AI yardımıyla geliştirilmiştir.

<!-- Ekran görüntüleri: dosyaları docs/screenshots/ altına koyup aşağıyı açın.
<p align="center">
  <img src="docs/screenshots/main.png" alt="Ana pencere" width="900">
</p>
-->

---

## İçindekiler

- [Neden](#neden)
- [Öne çıkan özellikler](#öne-çıkan-özellikler)
- [Kurulum](#kurulum)
- [Beş dakikada tipik işler](#beş-dakikada-tipik-işler)
- [Tüm özellikler (menü haritası)](#tüm-özellikler-menü-haritası)
- [Komut satırı modu](#komut-satırı-modu)
- [Nasıl çalışıyor](#nasıl-çalışıyor)
- [Derleme](#derleme)
- [Proje yapısı](#proje-yapısı)
- [Dosya konumları](#dosya-konumları)
- [Bilinen sınırlar](#bilinen-sınırlar)
- [Sık sorulanlar](#sık-sorulanlar)
- [Lisans ve bileşenler](#lisans-ve-bileşenler)
- [English summary](#english-summary)

---

## Neden

Bir Windows kurulum medyasını elden geçirmek bugün hâlâ dağınık bir iş: ISO'yu bir araçla
açarsınız, `install.wim`'i başka bir araçla düzenlersiniz, ESD'ye çevirmek için üçüncüsünü,
tekrar ISO yapmak için `oscdimg` komut satırını, USB'ye yazmak için dördüncüsünü
kullanırsınız. Üstelik image'ın içine dokunmanın klasik yolu DISM'in
**mount → düzenle → unmount** döngüsüdür: yavaş, yarıda kalırsa sistemde asılı kalan bir
mount noktası bırakır ve yönetici hakkı ister.

WIMTOOLBOX bu zincirin tamamını tek pencereye alır ve image'ın içine **mount etmeden**
yazar (`wimlib_update_image`). Bütün uzun işlemler iptal edilebilir, gerçek ilerleme
(yüzde, MB/sn, kalan süre) gösterir ve arayüzü hiçbir zaman kilitlemez.

---

## Öne çıkan özellikler

| | |
|---|---|
| 📀 **ISO çalışma alanı** | ISO açıldığında `sources` altındaki **tüm** WIM/ESD'ler (install + boot.wim) çıkarılıp o ISO'ya ait ayrı bir pencerede listelenir. Aynı ISO ikinci açılışta önbellekten gelir. |
| ✏️ **Mount'suz içerik düzenleme** | Image'ın İÇİNE dosya/klasör ekleme, silme, yeniden adlandırma. İçerik sekmesinde sağ tık ya da listeye sürükle-bırak. |
| 🗝️ **Image içi kayıt defteri** | `SOFTWARE`, `SYSTEM`, `DEFAULT`, `SAM`, `SECURITY`, `NTUSER.DAT` dosyaları mount edilmeden açılır, düzenlenir, tek adımda geri yazılır. `.reg` içe aktarma ve dosyayı toptan değiştirme dahil. |
| 🔁 **WIM ↔ ESD dönüştürme** | LZX / XPRESS / LZMS / solid; bölme (SWM) ve parçaları birleştirme; bütünlük tablosu. |
| 💿 **ISO üretme** | Kaynak ISO'yu sormadan tek adımda güncelleme, elle seçerek güncelleme ya da sıfırdan yeniden paketleme. Önyükleme yapısı korunur, **kaynak ISO asla değiştirilmez**. |
| 🔌 **Önyüklenebilir USB** | Dört bölüm şeması, canlı günlük, hız/ETA, USB bağlantı hızı göstergesi. 4 GB'ı aşan `install.wim` FAT32'de otomatik SWM'ye bölünür. |
| 💽 **Diske / VHD'ye kurulum** | Image'ı fiziksel diske kurma ya da yeni bir VHDX içine kurup **native boot** girişi ekleme — konak diskin bölümlerine dokunmadan. |
| 🧭 **Boot Manager** | BCD girişlerini yeniden adlandırma, varsayılan/zaman aşımı, gelişmiş önyükleme seçenekleri (msconfig karşılığı), yedekleme/geri yükleme — `bcdedit` çıktısı ayrıştırılmadan. |
| 🔍 **İnceleme araçları** | Image içinde arama (Ctrl+F), iki image'ı SHA-1 ile karşılaştırma, blob paylaşım analizi ("bu image'ı silsem kaç GB kazanırım"), SHA-256 raporu, dosya önizleme. |
| 🛟 **Güvenlik ağı** | Veri yok eden işlemlerden önce yedek, tip-yazarak-onay, bellek koruması, ayrıntılı günlük. |
| 🌍 **Türkçe / English** | 1850+ metin, anında dil değişimi, her denetimde açıklayıcı ipucu. |

---

## Kurulum

**Gereksinimler**

| | |
|---|---|
| İşletim sistemi | Windows 10 (1607+) veya Windows 11 — **x64** |
| Çalışma zamanı | [.NET 10 Desktop Runtime (x64)](https://dotnet.microsoft.com/download/dotnet/10.0) |
| Yönetici hakkı | Uygulama **her açılışta UAC ister** (bkz. aşağıdaki not) |
| İsteğe bağlı | ISO üretmek için [Windows ADK — Deployment Tools](https://learn.microsoft.com/windows-hardware/get-started/adk-install) (`oscdimg.exe`) |

**Çalıştırma**

1. Sürümler (Releases) bölümünden `WIMTOOLBOX.exe` dosyasını indirin.
2. Çift tıklayın. Kurulum gerektirmez, taşınabilirdir; ayarlarını `%APPDATA%` altında tutar.

> **Neden yönetici?** ACL'leri koruyarak yakalama/uygulama, diske kurulum, VHD oluşturma ve
> BCD yazma işlemlerinin hepsi yükseltilmiş hak ister. Uygulama bu yüzden manifestinde
> `requireAdministrator` ile gelir. Bunun bilinen bedeli şudur: yükseltme isteyen bir exe
> başka bir süreçten `CreateProcess` ile başlatılamadığı için **komut satırı modu betikten
> çağrılamaz** ve dosya ilişkilendirmesiyle çift tıklanan bir dosya, açık olan yükseltilmiş
> örneğe UIPI nedeniyle iletilemeyebilir.

---

## Beş dakikada tipik işler

<details open>
<summary><b>ISO'daki install.wim'i düzenleyip yeni ISO üretmek</b></summary>

1. `ISO > ISO Dosyası Aç...` — ISO'nun `sources` klasöründeki tüm image'lar çıkarılır ve
   o ISO'ya ait ayrı bir çalışma alanı penceresi açılır.
2. **İçerik** sekmesinde image'ın içine dosya sürükleyin, gereksizleri sağ tıkla silin.
3. `Arşiv > Kaynak ISO'yu Güncelle...` — kaynak ISO sorulmaz; hangi dosyanın nereye
   yazılacağı onay ekranında satır satır gösterilir. Çıktı **yeni** bir ISO'dur.

</details>

<details>
<summary><b>WinPE'ye (boot.wim) sürücü/araç eklemek</b></summary>

1. ISO'yu açın — `boot.wim` de listeye gelir, ayrıca açmanız gerekmez.
2. `boot.wim`'i seçip **İçerik** sekmesinden dosyalarınızı ekleyin.
3. `Arşiv > Kaynak ISO'yu Güncelle...` — değişmiş image'ların hepsi **tek ISO turunda**
   geri yazılır; değişmemiş olanlar boşuna yeniden yazılmaz.

</details>

<details>
<summary><b>install.wim'i ESD'ye çevirip ISO'yu küçültmek</b></summary>

1. Alttaki **Çıktı Ayarları**'nda Biçim = `ESD` seçin (sıkıştırma otomatik LZMS + solid olur).
2. Araç çubuğunda **Sıkıştır / Dönüştür**.
3. `Arşiv > Kaynak ISO'yu Güncelle...` — hedefin adı korunur: `install.wim` → `install.esd`.

</details>

<details>
<summary><b>ISO'yu önyüklenebilir USB'ye yazmak</b></summary>

1. `ISO > ISO'yu USB'ye Yaz (Önyüklenebilir)...`
2. Aygıtı seçin — satıcı/ürün/seri no, veri yolu, sektör düzeni ve **USB bağlantı hızı**
   (çalışılan ve desteklenen) gösterilir.
3. Şemayı seçin, hedefin adını **elle yazarak** onaylayın, yazmayı başlatın.

| Şema | Açılır | Not |
|---|---|---|
| GPT + FAT32 | UEFI | Varsayılan; 4 GB'ı aşan `install.wim` SWM'ye bölünür |
| MBR + FAT32 | BIOS/CSM + UEFI | Bölüm `active` işaretlenir |
| MBR + NTFS | yalnızca BIOS/CSM | `install.wim` bölünmez |
| GPT + FAT32 & NTFS | UEFI | Küçük FAT32 önyükleme bölümü + kalanı NTFS; bölme yok |

</details>

<details>
<summary><b>İkinci Windows'u diski bölmeden kurmak (native boot)</b></summary>

1. Image'ı seçip `Image > VHD'ye Kur (Native Boot)...`
2. Yol, boyut ve ayırma tipini verin; "önyükleme menüsüne ekle" işaretli kalsın.
3. Konak makinenin hiçbir diski bölümlenmez; ikinci Windows tek bir `.vhdx` dosyasında
   yaşar, dosyayı silmek kurulumu geri alır.

</details>

---

## Tüm özellikler (menü haritası)

### Dosya

| Komut | Ne yapar |
|---|---|
| WIM/ESD/ISO Aç… `Ctrl+O` | WIM / ESD / SWM / ISO açar; birden çok dosya listede durur. Sürükle-bırak ve son kullanılanlar listesi vardır |
| Klasörden Yeni WIM Oluştur… `Ctrl+N` | Klasörü yakalar; dışlama listesi (DISM `WimScript.ini` karşılığı) düzenlenebilir |
| Toplu İş Kuyruğu… | Birden çok dosyaya sırayla doğrulama / yeniden paketleme; bir dosya hata verse de kuyruk durmaz |
| SHA-256 Raporu… | `sha256sum` biçiminde özet dosyası üretir |
| Yedekler… | Alınan yedekleri listeler, geri yükler, siler |

### Image

| Komut | Ne yapar |
|---|---|
| Metadata'yı Düzenle… `F2` | `NAME`, `DESCRIPTION`, `DISPLAYNAME`, `DISPLAYDESCRIPTION`, `FLAGS` |
| Image'ı Sil / Önyüklenebilir İşaretle | Yerinde değişiklik; silmeden önce yedek teklif edilir |
| Klasöre Uygula… | DISM `/Apply-Image` karşılığı |
| Diske Uygula (Kur)… | Fiziksel diske kurulum: bölüm oluşturma/biçimlendirme + `bcdboot` ile önyükleme kaydı. Sistem diski hedef olamaz |
| VHD'ye Kur (Native Boot)… | VHDX/VHD oluşturur, image'ı uygular, önyükleme girişini ekler |
| Var Olan Kurulumu Boot Manager'a Ekle… | Diskteki bir Windows'u önyükleme menüsüne kaydeder |
| Windows Boot Manager'ı Yönet… | BCD girişleri: yeniden adlandırma, varsayılan, zaman aşımı, silme, onarım, yedek/geri yükleme, gelişmiş önyükleme seçenekleri |
| Image İçinde Ara… `Ctrl+F` | Ağacı ada göre tarar; `*`/`?` desteklenir, bulunanlar doğrudan çıkarılabilir |
| Image'ları Karşılaştır… | İki image'ın ağaçlarını **SHA-1** ile karşılaştırır (aynı boyutta farklı içerik kaçmaz) |
| Blob Paylaşım Analizi… | Her akışın kaç image'dan referans aldığını hesaplar; "silinirse kazanç" sütunu yalnızca tek image'a ait akışları sayar |
| Image İçi Kayıt Defteri… | Kayıt defteri dosyalarını mount etmeden açar/düzenler; `.reg` içe aktarma, dosyayı değiştirme, gerçek sürüm (UBR dahil) özeti |
| İşaretlileri Dışa Aktar… `Ctrl+E` | Seçili image'ları yeni dosyaya aktarır |
| Klasörü Yeni Image Olarak Ekle… | Açık WIM'e yeni image ekler |

### Arşiv

| Komut | Ne yapar |
|---|---|
| Başka WIM ile Birleştir… | Ad çakışmaları `(2)`, `(3)` ile çözülür |
| Yeniden Paketle / Dönüştür (WIM ↔ ESD) | LZX / XPRESS / LZMS, solid, bütünlük tablosu |
| Parçalara Böl (SWM)… | FAT32 / DVD5 / DVD9 ön ayarları |
| SWM Parçalarını Birleştir… | Parçalardan tek dosya üretir |
| Bütünlüğü Doğrula | Integrity table + akış SHA-1; bozuksa **hangi dosyanın** bozuk olduğunu raporlar |

### ISO

| Komut | Ne yapar |
|---|---|
| ISO Dosyası Aç… | `sources` altındaki tüm WIM/ESD'leri çıkarır, ayrı çalışma alanı penceresi açar |
| Kaynak ISO'yu Güncelle… | Açık dosyanın geldiği ISO'yu sormadan bulur; değişmiş tüm image'ları tek turda geri yazar |
| Başka Bir ISO Dosyasını Güncelle… | Kaynağı ve içeriği elle seçerek aynı işi yapar |
| ISO'yu Yeniden Oluştur… | Kaynağı DiscUtils ile okur, `oscdimg` ile sıfırdan paketler |
| Sürüm Ayarları (ei.cfg / PID.txt)… | Çok sürümlü ISO anahtarı: `ei.cfg` yoksa Setup tüm sürümleri listeler |
| ISO'yu USB'ye Yaz (Önyüklenebilir)… | Bkz. yukarıdaki şema tablosu |
| ISO Önbelleği… | Önbellekte ne birikmiş gösterir; **düzenlenmiş ama henüz bir ISO'ya yazılmamış** dosyalar ayrı renkle işaretlenir ve toplu temizliğe girmez |

### Görünüm

Dil (Türkçe / English), yedekleme tercihi (sor / her zaman / hiç), uzun işlem bitince
bildirim, dosya ilişkilendirmesi, gelişmiş disk/boot komutları, günlük klasörü, yenile `F5`.

### Sekmeler ve kısayollar

**Sürümler** (index, ad, mimari, sürüm, edition, boyut, dosya/klasör sayısı, önyükleme) ·
**İçerik** (image içi dosya ağacı, çıkarma, önizleme, kısayol üretme) · **Bilgi** (tüm
wimlib/XML alanları) · **XML** (ham metadata) · **Günlük** (canlı işlem günlüğü)

`Ctrl+O` aç · `Ctrl+N` klasörden WIM · `Ctrl+E` dışa aktar · `Ctrl+F` image içinde ara ·
`F2` metadata · `F5` yenile · `Esc` süren işlemi iptal et

---

## Komut satırı modu

```
WIMTOOLBOX.exe --info    <wim>
               --verify  <wim>
               --export  <wim> <1,2|all> <çıkış> [--compress none|xpress|lzx|lzms] [--solid] [--integrity] [--threads N]
               --apply   <wim> <index> <klasör> [--noacls]
               --split   <wim> <ilk-parça.swm> <parçaMB> [--integrity]
```

Çıkış kodu: `0` başarılı · `1` kullanım hatası · `2` işlem başarısız.

Pencere açılmaz; çıktı `AttachConsole` ile çağıran terminale bağlanır (terminal yoksa
yutulur, çıkış kodu yine doğrudur).

> **Not:** Uygulama `requireAdministrator` manifestiyle geldiği için bu mod **yükseltilmiş
> bir terminalden elle** çalıştırılabilir; betik/otomasyon içinden `CreateProcess` ile
> başlatılamaz. Otomasyon gerekiyorsa manifestteki `requestedExecutionLevel` değerini
> `asInvoker` yapıp kendi derlemenizi alın.

---

## Nasıl çalışıyor

**Motor.** Tüm WIM/ESD işlemleri [wimlib](https://wimlib.net/) ile yapılır; erişim, projeye
ait `WimLibNet` P/Invoke sarmalayıcısı üzerindendir. `libwim.dll` sarmalayıcının içine
gömülü kaynak olarak taşınır ve ilk çağrıda geçici bir yola çıkarılır — yanınızda taşımanız
gereken ayrı bir native dosya yoktur.

**Mount yok.** Windows'ta wimlib mount edemez (FUSE desteği Linux'a özgüdür), bu yüzden
image'ın içine yazmanın yolu `wimlib_update_image`'dir. Değişiklikler tek toplu çağrıda
uygulanır, yani dosya bir kez yeniden yazılır. Silinen içeriğin blokları dosyada ölü alan
olarak kalır; yeri geri kazanmak "Yeniden Paketle" işidir.

**Kayıt defteri.** Yeni bir motor yoktur: dosya wimlib ile geçici klasöre çıkarılır, projenin
kendi REGF ayrıştırıcı/yazıcısıyla okunup düzenlenir ve `wimlib_update_image` ile geri
konur. Neden XML yetmiyor: XML, imajı üreten aracın *beyanıdır*; güncelleme revizyonu
(`UBR`) orada **hiç yoktur**, kayıt defteri ise Windows'un kendi kaydıdır.

**ISO.** Okuma `DiscUtils.Iso9660` / `DiscUtils.Udf` ile, üretme Windows ADK'daki
`oscdimg.exe` ile yapılır (düşük seviye UDF/El Torito sektör yazmak yerine Microsoft'un
desteklenen paketleyicisi). Kaynak ISO hiçbir akışta değiştirilmez; çıktı her zaman yeni
bir dosyadır. Çıkarılan her dosyanın **ISO içindeki yolu** hatırlanır, böylece `boot.wim`
açıkken yapılan güncelleme kurulum image'ının üzerine yazmaz.

**Disk, USB ve BCD.** Aygıt bilgisi WMI'dan değil doğrudan `DeviceIoControl` ile okunur
(satıcı/ürün/seri no, veri yolu, kapasite, mantıksal+fiziksel sektör, bölüm tablosu, yazma
koruması). USB bağlantı hızı, aygıtın takılı olduğu hub'ın port kaydından çözülür ve
doğrulanmamış bir port için hız **hiç gösterilmez**. BCD okuma canlı kayıt defterinden,
yazma element düzeyinde native API ile yapılır — `bcdedit.exe` çıktısı hiçbir yerde
ayrıştırılmaz (dile bağımlıdır, sessizce bozulur). Her yıkıcı BCD işleminden önce canlı
depo koşulsuz yedeklenir.

**Bellek koruması.** wimlib, solid LZMS'te iş parçacığı başına yüzlerce MB çalışma tamponu
ister; "tüm çekirdekler" ayarı 32 çekirdekli bir makinede bunu on gigabaytlara çıkarır.
`MemoryGuard` iş parçacığı başına gerçek maliyeti ölçer, boş belleğin yarısından bir pay
ayırarak bütçeler ve gerekirse sayıyı kısar — kıstığında bunu arayüzde ve günlükte söyler.
Sayfa dosyası kapalıysa durum çubuğu uyarır.

**Kararlılık.** Her uzun işlem iptal edilebilir; UI thread'i hiçbir işlemde bloklanmaz;
yerinde değiştiren her işlemden sonra dosya taze bir tanıtıcıyla yeniden açılır; global bir
hata ağı, bir işleyiciden kaçan hatanın süreci öldürmesini engeller.

> Kararların ayrıntılı gerekçeleri ve doğrulama notları için: [`PROPOSALS.md`](PROPOSALS.md)

---

## Derleme

> ⚠️ **Bu depo tek başına derlenmez.** Proje, kardeş klasördeki `WimLibNet`
> sarmalayıcısına proje referansıyla bağlıdır: `..\WimLibNet\WimLibNet.csproj`
> (bu depoda yer almaz). Derlemeden önce onu WIMTOOLBOX klasörünün yanına yerleştirin.

```
D:\PROJELER\
├── WIMTOOLBOX\      ← bu depo
└── WimLibNet\       ← wimlib P/Invoke sarmalayıcısı (native x64 libwim.dll gömülü)
```

**Gereksinimler:** .NET 10 SDK · Visual Studio 2026 ya da `dotnet` CLI · x64

```powershell
dotnet build   "WIMTOOLBOX.vbproj" -c Release
dotnet run     --project "WIMTOOLBOX.vbproj" -c Debug
```

Derleme `Option Strict On` ile yapılır ve uyarısız geçmelidir.

**Tek dosyalık dağıtım**

```powershell
dotnet publish "WIMTOOLBOX.vbproj" -p:PublishProfile=win-x64-singlefile
```

Çıktı: `publish\win-x64\WIMTOOLBOX.exe` — tek dosya, çerçeveye bağımlı (birkaç MB); hedef
makinede **.NET 10 Desktop Runtime (win-x64)** kurulu olmalıdır. Kendi kendine yeten
(self-contained) sürüm çalışma zamanını da gömdüğü için exe ~50 MB'tan başlıyordu; bilinçli
olarak çerçeveye bağımlı tercih edildi.

---

## Dosya konumları

| Ne | Nerede |
|---|---|
| Ayarlar | `%APPDATA%\WIMTOOLBOX\settings.json` |
| Günlük | `logs\wimtoolbox.log` (uygulama klasörü yazılamazsa `%LOCALAPPDATA%\WIMTOOLBOX\logs`) — 2 MB'ta döner, 2 yedek |
| ISO önbelleği | `%LOCALAPPDATA%\WIMTOOLBOX\iso-extract` (ISO Önbelleği ekranından yönetilir) |
| WIM yedekleri | Kaynak dosyanın yanındaki gizli `.wimtoolbox-backups` klasörü (dosya başına en yeni 3) |
| BCD yedekleri | `%APPDATA%\WIMTOOLBOX\BcdBackups` |

---

## Bilinen sınırlar

- **Image mount edilmez.** İçerik değiştirme, kayıt defteri düzenleme ve dosya çıkarma
  mount'suz çalışır; ancak bir image'ı sürücü harfi altında canlı klasör gibi göstermek
  mümkün değildir — buna ihtiyaç duyan senaryolarda DISM gerekir.
- **Adım adım geri alma (undo) yoktur.** Geri dönüş yolu yedeklerdir ve yedek yalnızca veri
  yok eden işlemlerden (image silme, içerikten silme) önce, onayınızla alınır.
- **SWM parçaları tek başına eksiktir.** Kardeş parçalar bağlanınca okuma/çıkarma/uygulama
  çalışır; birleştirme, yeniden paketleme ve bölme için önce tek dosya üretilmelidir.
- **ISO üretimi Windows ADK ister** (`oscdimg.exe`) ve ISO boyutu kadar geçici disk alanı
  kullanır.
- **32 GiB üstü FAT32 birim oluşturulamaz** (Windows'un kısıtı). Daha büyük USB'lerde FAT32
  seçilirse bölüm bu sınırda kesilir; tüm alanı kullanmak için "GPT + FAT32 & NTFS" şemasını
  ya da ikinci veri bölümü seçeneğini kullanın.
- **Yalnızca x64.** `WimLibNet`'in native bağımlılığı şu an yalnızca x64 derleniyor.
- Katı (solid) kaynaklarda blob analizinin sıkıştırılmış rakamları yaklaşıktır; açılmış
  toplam her koşulda kesindir.

---

## Sık sorulanlar

<details>
<summary><b>DISM yerine bunu kullanmam ne kazandırır?</b></summary>

Mount/unmount döngüsü yok (yarıda kalan mount noktası da yok), her işlem iptal edilebilir,
gerçek ilerleme/ETA var, ISO ve USB adımları aynı programda ve WIM ↔ ESD dönüşümü tek
düğme. DISM'in yerini tümüyle almaz: canlı bir mount noktası gereken senaryolarda hâlâ DISM
gerekir.
</details>

<details>
<summary><b>Kaynak ISO'm bozulur mu?</b></summary>

Hayır. ISO akışlarının hepsi çıktı olarak **yeni** bir dosya üretir; kaynak ISO hiçbir
adımda değiştirilmez.
</details>

<details>
<summary><b>ISO önbelleği neden gigabaytlar tutuyor, silsem olur mu?</b></summary>

Önbellek, aynı ISO'yu ikinci açışınızda 6 GB'ı yeniden çıkarmamak içindir. `ISO > ISO
Önbelleği...` ekranından yönetin: "Değiştirilmemiş" dosyaları silmek yalnızca yer açar
(gerektiğinde kaynaktan yeniden çıkarılır), **"Düzenlenmiş" işaretli** dosyaları silmek ise
henüz bir ISO'ya yazılmamış çalışmanızı yok eder.
</details>

<details>
<summary><b>"Yeniden paketledim ama dosya küçülmedi/büyüdü"</b></summary>

WIM'den içerik silmek dosyada ölü alan bırakır (biçim delik açmaya izin vermez); yeri geri
kazanan işlem yeniden paketlemedir. ESD'ye çevirmek en büyük kazancı verir ama LZMS + solid
sıkıştırma belirgin şekilde yavaştır ve çok bellek ister.
</details>

<details>
<summary><b>Uygulama neden her açılışta UAC istiyor?</b></summary>

Diske kurulum, VHD, BCD ve ACL'li apply işlemleri yükseltilmiş hak gerektirdiği için
manifest `requireAdministrator` olarak ayarlıdır. Yalnızca inceleme/dönüştürme yapacaksanız
manifestteki değeri `asInvoker` yapıp kendi derlemenizi alabilirsiniz.
</details>

---

## Lisans ve bileşenler

| Bileşen | Rol | Lisans |
|---|---|---|
| [wimlib](https://wimlib.net/) | WIM/ESD motoru (`WimLibNet` üzerinden) | LGPL-3.0-or-later |
| [DiscUtils](https://github.com/DiscUtils/DiscUtils) `Iso9660`, `Udf` | ISO/UDF okuma | MIT |
| Windows ADK `oscdimg.exe` | ISO paketleme (isteğe bağlı, dışarıdan çağrılır) | Microsoft ADK koşulları |

REGF (kayıt defteri), BCD, El Torito, MS-SHLLINK ve FAT32 biçimleri üçüncü parti kütüphane
kullanılmadan, biçim belgelerine göre bu projede uygulanmıştır.

---

## English summary

**WIMTOOLBOX** is a Windows Forms (VB.NET / .NET 10, x64) toolbox for Windows installation
images and ISOs, powered by [wimlib](https://wimlib.net/).

- Open WIM / ESD / SWM / **ISO** — every WIM/ESD under the ISO's `sources` (install *and*
  `boot.wim`) is extracted into a dedicated workspace window, cached for the next open.
- **Edit image contents without mounting** (`wimlib_update_image`): add, delete and rename
  files inside an image; drag & drop supported.
- **Edit registry hives inside an image** (`SOFTWARE`, `SYSTEM`, `SAM`, `NTUSER.DAT`, …)
  without mounting — including `.reg` import and whole-hive replacement — using the
  project's own REGF parser/writer.
- Convert **WIM ↔ ESD** (LZX / XPRESS / LZMS / solid), split to SWM, join, verify integrity.
- **Rebuild or update ISOs** (`oscdimg`, Windows ADK) — the source ISO is never modified.
- **Write bootable USB** with four partition schemes, live log, speed/ETA and USB link-speed
  detection; oversized `install.wim` is split to SWM automatically on FAT32.
- **Apply to disk** or **install to a VHDX for native boot**, plus a full **BCD editor** that
  never parses `bcdedit` output.
- Compare images by SHA-1, search inside images, blob-sharing analysis, SHA-256 reports,
  file preview, batch queue, backups, CLI mode.
- Fully bilingual UI (Turkish / English), switchable at runtime.

**Build note:** this repository does not build on its own — it project-references
`..\WimLibNet\WimLibNet.csproj` (a plain P/Invoke wrapper embedding an x64 `libwim.dll`),
which must sit next to this folder. Requires the .NET 10 Desktop Runtime (x64); the app
requests administrator rights at startup.
