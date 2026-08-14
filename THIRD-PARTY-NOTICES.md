# Üçüncü Parti Bildirimleri / Third-Party Notices

WIMTOOLBOX'ın kendi kodu telif hakkıyla korunur ve açık kaynak değildir; kullanım
koşulları için bkz. [`LICENSE`](LICENSE). Uygulama, aşağıdaki üçüncü parti bileşenlerle
birlikte çalışır ve dağıtılır; her biri **kendi lisansı** altındadır ve bu lisansların
verdiği haklar WIMTOOLBOX'ın kendi koşullarıyla kısıtlanmaz.

WIMTOOLBOX's own code is proprietary and not open source; see [`LICENSE`](LICENSE) for
its terms. The application ships with the third-party components listed below, each
under **its own license**; nothing in WIMTOOLBOX's terms restricts the rights those
licenses grant.

---

## wimlib

- **Rol / Role:** WIM/ESD motoru — `WimLibNet` P/Invoke sarmalayıcısı üzerinden çağrılan
  `libwim.dll` / the WIM/ESD engine (`libwim.dll`, called through the `WimLibNet`
  P/Invoke wrapper)
- **Lisans / License:** GNU Lesser General Public License, sürüm 3.0 veya sonrası
  (LGPL-3.0-or-later)
- **Kaynak / Source:** <https://wimlib.net/> · <https://wimlib.net/git/>
- **Telif / Copyright:** © Eric Biggers ve wimlib katkıcıları

**Bağlama biçimi / Linking:** WIMTOOLBOX, `libwim.dll`'i **dinamik olarak** yükler
(P/Invoke). DLL, `WimLibNet.dll` içinde gömülü kaynak olarak taşınır ve ilk kullanımda
geçici bir yola çıkarılıp `NativeLibrary.SetDllImportResolver` ile çözülür. wimlib'in
kaynak kodu değiştirilmemiştir.

WIMTOOLBOX dynamically loads `libwim.dll` via P/Invoke. The DLL is carried as an embedded
resource inside `WimLibNet.dll` and extracted to a temporary path on first use. wimlib's
source code is not modified.

**İkili dağıtırken / When redistributing binaries:** LGPL-3.0 §4 uyarınca dağıtımla
birlikte (a) wimlib kullanıldığı bildirilmeli, (b) LGPL/GPL metinlerinin bir kopyası ya da
bağlantısı verilmeli, (c) kullanıcının wimlib'i değiştirilmiş bir sürümüyle değiştirebilmesi
için kaynak kodu ya da kaynağa erişim sunulmalıdır. Bu dosya (a) ve (b) maddelerini
karşılar; (c) için wimlib'in resmi kaynak dağıtımına yönlendirilir: <https://wimlib.net/>

- LGPL-3.0: <https://www.gnu.org/licenses/lgpl-3.0.html>
- GPL-3.0: <https://www.gnu.org/licenses/gpl-3.0.html>

---

## DiscUtils (DiscUtils.Iso9660, DiscUtils.Udf)

- **Rol / Role:** ISO9660 / UDF okuma — ISO'dan image çıkarma ve yeniden paketleme
- **Lisans / License:** MIT
- **Kaynak / Source:** <https://github.com/DiscUtils/DiscUtils>
- **Telif / Copyright:** © Kenneth Bell ve DiscUtils katkıcıları

---

## System.Management (.NET)

- **Rol / Role:** Storage Management WMI sağlayıcısına erişim (disk/bölüm/birim işlemleri)
- **Lisans / License:** MIT
- **Kaynak / Source:** <https://github.com/dotnet/runtime>
- **Telif / Copyright:** © .NET Foundation ve katkıcıları

---

## Windows ADK — `oscdimg.exe`

- **Rol / Role:** ISO paketleme (isteğe bağlı; WIMTOOLBOX ile **dağıtılmaz**, kullanıcının
  makinesinde kuruluysa dışarıdan çağrılır)
- **Lisans / License:** Microsoft Windows ADK lisans koşulları
- **Kaynak / Source:** <https://learn.microsoft.com/windows-hardware/get-started/adk-install>

---

## Kendi uygulamalarımız / Implemented in this project

Aşağıdaki biçimler üçüncü parti kütüphane kullanılmadan, biçim belgelerine göre bu projede
uygulanmıştır; bu kod WIMTOOLBOX'a aittir ve [`LICENSE`](LICENSE) koşullarına tabidir:

The following formats are implemented from their specifications inside this project, with
no third-party library; that code belongs to WIMTOOLBOX and is governed by
[`LICENSE`](LICENSE):

- REGF (Windows kayıt defteri dosyası biçimi) — okuma ve yazma
- BCD (Boot Configuration Data) deposu — okuma, yazma, VHD cihaz baytları
- El Torito / UDF yapılarının okunması
- MS-SHLLINK (`.lnk`) üretimi
- FAT32 (Microsoft "fatgen103" belgesi)

**Not:** BIOS/CSM önyükleme sektörü kodu bilinçli olarak projeye gömülmemiştir; gerekirse
sistemde ya da ADK'de bulunan Microsoft `bootsect.exe` çağrılır.
