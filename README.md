# Lunix 60Hz Takılı Kalma — Detaylı Çözüm Rehberi / Detailed Fix Template

<!-- keywords: Lunix 60Hz, linux 60hz stuck, 60hz stuck, refresh rate stuck linux, hyprland monitor -->

1. Özet / TL;DR
- Türkçe: Bu rehber, ekran yenileme oranının 60Hz'de takılı kalması sorununu tanımlamak, kök nedenini analiz etmek ve kalıcı bir çözüm uygulamak için yapılandırılmıştır. "Fix (Adım Adım)" bölümüne kendi çözümünüzü yapıştırın.
- English: This template helps capture a reproducible fix for the refresh rate stuck at 60Hz. Paste your step-by-step commands and validation results under the "Fix (Step-by-step)" section.

2. Hedef Sistemler / Affected Systems
- Distro: Lunix (veya Debian/Ubuntu/Fedora türevleri)
- Grafik sürücü: Intel (i915), AMD (amdgpu), NVIDIA (proprietary) — lütfen kullandığınız sürücüyü belirtin.
- Display protocol: Xorg or Wayland (hyprland / sway etc.)

3. Belirtiler / Symptoms
- Sistem ekran yenileme oranını 60Hz olarak gösteriyor ve diğer değerleri kabul etmiyor.
- DE (GNOME/KDE) veya xrandr, hyprctl ile yapılan değişiklikler kalıcı olmuyor.
- Oyunlarda/uygulamalarda tazeleme oranı istenen değere gelmiyor.

4. Ön Koşullar / Preconditions (toplayın)
- `xrandr --verbose` veya `hyprctl monitors all` çıktısı
- `/var/log/Xorg.0.log` veya `journalctl -b` içinde ilgili sürücü hataları
- Monitör model ve seri numarası (OSD veya üretici etiketi)
- Kablo tipi: DisplayPort / HDMI ve adaptör kullanımı

5. Tekrarlanabilirlik / Steps to Reproduce
1) Monitörü yüksek tazeleme oranına ayarlayın (örn. 144Hz).
2) Masaüstü ortamı veya xrandr/hyprctl aracılığıyla 60Hz'e geri dönün.
3) Değişikliğin uygulanmadığını veya sistemin 60Hz'de kaldığını gözlemleyin.

6. Kök Neden / Root Cause (analiz alanı)
- EDID okunamaması / yanlış EDID bilgisi
- Sürücü (driver) varsayılan modu zorlaması
- Hyprland / Xorg konfigürasyonundaki modeline/PreferredMode ayarları
- Kablo veya port kısıtlaması (ör. bazı HDMI kablolar yüksek Hz'i desteklemez)

7. Fix (Step-by-step) — Buraya kendi çözümünüzü ekleyin
Lütfen aşağıdaki şablonu kendi komut ve çıktılarınızla doldurun. Her adımı kısa bir açıklama ile destekleyin.

Örnek (hyprland özelinde)

1) Mevcut monitör durumunu alın
```bash
hyprctl monitors all
# veya
xrandr --verbose
```

2) EDID ve desteklenen modları kontrol edin
```bash
sudo cat /sys/class/drm/card0/card0-eDP-1/edid  # (yol örnek olabilir)
# ya da
sudo parse-edid <(get-edid)
```

3) Manuel model ekleme (örnek xrandr)
```bash
cvt 1920 1080 144
# çıkan modeline satırını kullanarak
xrandr --newmode "1920x1080_144.00" 338.25 1920 2080 2296 2672 1080 1083 1088 1120 -hsync +vsync
xrandr --addmode DP-1 "1920x1080_144.00"
xrandr --output DP-1 --mode "1920x1080_144.00"
```

4) Kalıcı yapılandırma (hyprland örneği)
```text
# ~/.config/hypr/config/monitors.lua
hl.monitor({
    output   = "eDP-1",
    mode     = "1920x1080@144.0",
    position = "0x0",
    scale    = 1.0,
    vrr      = 0,
})
```

5) Uygulama ve yeniden doğrulama
```bash
hyprctl reload
hyprctl monitors
xrandr | grep " connected"
```

8. Doğrulama / Validation
- Beklenen sonuç: `hyprctl monitors` veya `xrandr` çıktısı yeni modu ve Hz'i gösteriyor.
- Test: tam ekran bir oyun veya vsync gerektiren uygulama ile tazeleme oranını gözlemleyin.

9. Örnek çıktı ekleyin (kopyala-yapıştır)
- `hyprctl monitors all` çıktısı
- `xrandr --verbose` ilgili bölüm
- `dmesg` veya `journalctl` sürücü hataları

10. Troubleshooting / Ek notlar
- NVIDIA: nvidia-settings ile Xorg yapılandırmasını kontrol edin.
- Wayland: xrandr çalışmayabilir; Wayland uyumlu araçlar ve hyprland config tercih edin.
- Kablo/protocol sorunlarında farklı kablo ve portu deneyin.

11. Referanslar / References
- Kernel, sürücü veya monitör üreticisi dokümanlarına linkler ekleyin.

12. Yayınlama ve SEO notları
- Dosya başlığı ve README ilk paragrafında anahtar kelimeleri kullanın (örn. "Lunix 60Hz", "linux 60hz stuck").
- Rehberi hem Türkçe hem İngilizce kısa özetlerle sunmak arama görünürlüğünü artırır.

