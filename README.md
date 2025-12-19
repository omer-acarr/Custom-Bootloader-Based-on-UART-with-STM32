# STM32 Custom UART Bootloader (IAP)

Bu proje, STM32F4 serisi mikrodenetleyiciler için geliştirdiğim, harici bir programlayıcıya (ST-Link/J-Link) ihtiyaç duymadan, seri port (UART) üzerinden yazılım güncelleme (In-Application Programming - IAP) imkanı sağlayan bir Bootloader yazılımıdır.

Gömülü sistemlerin en kritik yetkinliklerinden biri olan "Memory Management" (Bellek Yönetimi) ve "Bare-metal" programlama prensiplerini derinlemesine kavramak amacıyla bu mimariyi tasarladım.

## 🎯 Projenin Amacı ve Motivasyonu
Gömülü yazılım mühendisliğinde, sahada çalışan bir cihazın kapağını açmadan güncellenebilmesi hayati bir gereksinimdir. Bu projede; standart HAL kütüphanelerinin ötesine geçerek, **Linker Script** manipülasyonu, **Flash Memory** sektör yönetimi ve **İşlemci Çekirdeği (ARM Cortex-M4)** kayıtçılarının (Register) doğrudan kontrolü üzerine yoğunlaştım.

Amacım, sadece çalışan bir kod yazmak değil, işlemcinin "Reset" anından itibaren ana uygulamaya (Application) geçiş sürecine kadar olan tüm "Boot" sürecini kontrol altına almaktır.

## ⚙️ Teknik Detaylar ve Mimari
Proje, mikrodenetleyicinin Flash belleğini iki ana bölüme ayırarak çalışır:

1.  **Bootloader Bölümü (Sector 0):** Cihaz enerjilendiğinde ilk çalışan koddur. Belirli bir süre (timeout) veya tetikleyici (buton/komut) bekler. Eğer güncelleme isteği varsa UART hattını dinler.
2.  **Application Bölümü (Sector 1+):** Kullanıcının asıl kodunun çalıştığı bölgedir.

### Öne Çıkan Teknik Yetkinlikler
* **Flash Bellek Yönetimi:** Flash belleğin sektör bazlı silinmesi (Erase) ve 4-byte/word tabanlı yazılması işlemleri, donanım hata bayrakları (Flags) kontrol edilerek güvenli bir şekilde gerçekleştirildi.
* **Linker Script Düzenleme:** `.ld` dosyasında hafıza haritası (Memory Map) yeniden düzenlenerek, Bootloader ve Application kodlarının çakışmaması sağlandı.
* **Jump to Application (Dallanma):** Bootloader görevini tamamladığında, `Function Pointer` kullanılarak işlemcinin Program Counter (PC) ve Stack Pointer (MSP) adresleri ana uygulamanın başlangıç adresine yönlendirildi.
* **Vector Table Relocation:** Kesme vektör tablosunun (Interrupt Vector Table) offset değeri, ana uygulamanın çalışabilmesi için dinamik olarak kaydırıldı (SCB->VTOR).

## 🛠 Kullanılan Teknolojiler ve Araçlar
* **Donanım:** STM32F4 Discovery Kit (ARM Cortex-M4)
* **Yazılım Dili:** Embedded C
* **IDE:** STM32CubeIDE
* **Haberleşme:** UART (Universal Asynchronous Receiver-Transmitter)
* **Test Araçları:** Tera Term / RealTerm (Binary veri transferi için)

## 🚀 Nasıl Çalışır?
1.  Cihaz başlatıldığında Bootloader devreye girer.
2.  Kullanıcı butonuna basılıysa veya UART üzerinden belirli bir "Handshake" baytı gelirse **Güncelleme Moduna** geçer.
3.  Bilgisayardan gönderilen yeni yazılımın `.bin` dosyası paketler halinde alınır.
4.  Gelen veri, CRC kontrolü yapılarak Flash belleğin ilgili sektörlerine yazılır.
5.  Yazma işlemi bittiğinde sistem Resetlenir veya doğrudan ana uygulamaya (Jump) sıçranır.

## 📈 Gelecek Geliştirmeler (To-Do)
* Veri transferinde AES-128 şifreleme ekleyerek güvenli boot (Secure Boot) altyapısı oluşturmak.
* Yazılımın bütünlüğünü doğrulamak için gelişmiş bir CRC-32 kontrolü entegre etmek.
* Haberleşme arayüzüne USB (DFU Class) desteği eklemek.

---
*Bu proje, gömülü sistemlerin düşük seviye (low-level) çalışma mantığını anlamak ve profesyonel firmware güncelleme standartlarını uygulamak için Ömer Faruk Acar tarafından geliştirilmiştir.*
