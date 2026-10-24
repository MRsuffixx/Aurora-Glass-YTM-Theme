# Aurora Glass — YouTube Music özel teması

**Diller:** [English](README.md) · Türkçe (bu dosya)

YouTube Music için SCSS ile yazılmış, cam yüzeyli (glassmorphism), katmanlı gradient'li ve animasyonlu bir özel tema. Varsayılan SCSS dosyası temel alınmıştır; o dosyadaki tüm işlevsel düzeltmeler korunmuş, yalnızca görünüm yeniden tasarlanmıştır.

> Bu tema YouTube, Google veya YouTube Music ile bağlantılı değildir ve onlar tarafından onaylanmamıştır. Yalnızca arayüzün görünümünü değiştirir; uygulama davranışına dokunmaz.

---

## İçindekiler

- [Özellikler](#özellikler)
- [Gereksinimler](#gereksinimler)
- [Derleme](#derleme)
- [Yükleme](#yükleme)
- [Yapılandırma](#yapılandırma)
- [Dosya yapısı](#dosya-yapısı)
- [Seçici durumu: doğrulanmış ve varsayım](#seçici-durumu-doğrulanmış-ve-varsayım)
- [Performans ve erişilebilirlik](#performans-ve-erişilebilirlik)
- [Sorun giderme](#sorun-giderme)
- [Varsayılan dosyadan korunan ve düzeltilen noktalar](#varsayılan-dosyadan-korunan-ve-düzeltilen-noktalar)
- [Bilinen sınırlar](#bilinen-sınırlar)

---

## Özellikler

**Görünüm**

- Koyu, hafif mor tonlu zemin; birincil vurgu `#9b8cff`, ikincil vurgu `#ff7ab8`.
- Arka planda gradient'lerden oluşan, çok yavaş kayan aurora katmanı.
- Üst bar ve alt oynatıcıda gerçek `backdrop-filter` cam yüzeyi; menüler, diyaloglar, tooltip'ler ve arama önerileri de aynı camı kullanır.
- Kartlar, chip'ler ve sidebar yarı saydam dolgu, ince kenarlık ve üst kenar ışığıyla cam görünümü alır (blur yok, çünkü altlarında kayan içerik yok).
- Gradient dolgulu ilerleme ve ses çubukları, parlayan knob, cam diskli oynatma butonu.
- Seçili sidebar öğesi, kuyruk satırı ve chip'lerde gradient çerçeve veya dolgu.
- İnce, koyu temaya uygun scrollbar; seçili metin rengi; klavye focus halkası.

**Animasyon**

- Tek sürekli animasyon: aurora kayması (80 sn, yalnızca `transform`).
- Giriş: bölüm rafları kademeli yükselir, menü listesi yumuşak açılır.
- Hover: kapak zoom'u, alttan yükselen gradient overlay, oynatma butonu parıltısı, sidebar ve kuyruk öğelerinde hafif kayma, arama alanında focus halkası ve parıltı.
- Basılma hissi `scale` ile verilir.
- Tüm geçişler tek easing eğrisi ve üç merkezi süre kullanır; `transition: all` yoktur.

---

## Gereksinimler

- **Dart Sass 1.50 veya üstü** (`sass` komutu). Tema `@use` / `@import` gerektirmez.
- CSS yükleyebilen bir YouTube Music istemcisi ya da tarayıcı stil eklentisi.
- Chromium tabanlı bir motor önerilir. Tema `:is()`, `:has()`, `inset` ve tek tek `scale` / `translate` özelliklerini kullanır.

Sass kurulumu:

```bash
npm install -g sass
sass --version
```

---

## Derleme

```bash
# Okunabilir çıktı
sass --no-source-map youtube-music-theme.scss youtube-music-theme.css

# Küçültülmüş çıktı (yaklaşık 24 KB)
sass --no-source-map --style=compressed youtube-music-theme.scss youtube-music-theme.min.css

# Geliştirirken: dosya değişince otomatik derle
sass --watch --no-source-map youtube-music-theme.scss:youtube-music-theme.css
```

---

## Yükleme

Derlenen `.css` dosyasını, kullandığınız yöntemin özel CSS alanına ekleyin:

- **Özel CSS destekli masaüstü istemci:** İstemcinin ayarlarındaki özel CSS / tema bölümüne derlenmiş dosyayı ekleyin. Menü adı istemciye göre değişir.
- **Tarayıcı:** Stylus gibi bir kullanıcı stili eklentisinde yeni stil oluşturup CSS'i yapıştırın ve `music.youtube.com` için etkinleştirin.

Ekledikten sonra sayfayı yenileyin.

---

## Yapılandırma

### 1. Derleme zamanı anahtarları

Dosyanın başındaki üç anahtar `!default` ile tanımlıdır, yani dosyayı düzenlemeden dışarıdan değiştirebilirsiniz:

| Anahtar | Varsayılan | Ne yapar |
|---|---|---|
| `$enable-glass-blur` | `true` | `false` olursa hiçbir yerde `backdrop-filter` üretilmez, yüzeyler opak yedek renge düşer. |
| `$enable-ambient` | `true` | `false` olursa aurora arka plan katmanı üretilmez. |
| `$enable-ambient-motion` | `true` | `false` olursa aurora sabit kalır, kayma animasyonu üretilmez. |

Bir sarmalayıcı dosya ile değiştirmek için (`custom.scss`):

```scss
@use 'youtube-music-theme' with (
  $enable-ambient-motion: false,
  $enable-glass-blur: true
);
```

```bash
sass --no-source-map custom.scss custom.css
```

### 2. Renkler, yarıçaplar ve süreler

Bunlar `!default` değildir; doğrudan `youtube-music-theme.scss` içinde §2 "Design tokens" bölümünden değiştirin:

| Değişken | Anlamı |
|---|---|
| `$bg-0` … `$bg-3` | Zemin katmanları (en koyudan açığa) |
| `$text-primary`, `$text-secondary`, `$text-tertiary` | Metin renkleri |
| `$accent-1`, `$accent-2`, `$accent-3` | Birincil, ikincil ve aurora ışığı vurgusu |
| `$radius-xs` … `$radius-pill` | Köşe yuvarlaklığı ölçeği |
| `$dur-fast`, `$dur-base`, `$dur-slow` | Geçiş süreleri |
| `$ease-out`, `$ease-in-out` | Easing eğrileri |
| `$logo-hue` | Logo rengi (kırmızıyı mor aralığa çeviren `hue-rotate` değeri) |
| `$playerIconSize`, `$playerButtonSize` | Oynatıcı ikon ve buton boyutu (varsayılan dosyadan) |

Vurgu rengini değiştirirseniz `$grad-accent` ve `$grad-accent-soft` gradient'leri de otomatik güncellenir.

### 3. Çalışma zamanı değişkenleri

`ytmusic-app` üzerinde tanımlı CSS değişkenleridir; derleme gerekmeden DevTools'tan, bir betikle ya da ek bir CSS dosyasıyla değiştirilebilir:

| Değişken | Varsayılan | Kullanım |
|---|---|---|
| `--ytmx-glow` | vurgu rengi, %42 | Kapak, oynatma butonu ve oynatıcı çevresindeki parıltı rengi |
| `--ytmx-glow-2` | ikincil vurgu, %30 | İkincil parıltı |
| `--ytmx-dur-fast` / `-base` / `-slow` | 160 / 260 / 420 ms | Tüm tema geçişleri |
| `--ytmx-zoom` | `1.06` | Kart kapağı hover zoom'u (`1` yaparsanız zoom kapanır) |
| `--ytmx-blur-sm` / `-md` / `-lg` | 10 / 18 / 28 px | Blur yarıçapları |

**Albüm kapağından renk:** CSS, görselin piksellerini okuyamaz. Bu yüzden `--ytmx-glow` yalnızca bir kanca olarak bırakıldı. Kapaktan renk çıkarıp bu değişkeni ayarlayan küçük bir betik yazarsanız parıltılar şarkıya göre değişir; tema bunun için ek bir şey gerektirmez.

Örnek (tarayıcı konsolunda deneme):

```js
document.querySelector('ytmusic-app')
  .style.setProperty('--ytmx-glow', 'rgba(255, 140, 60, 0.45)');
```

---

## Dosya yapısı

Dosya bölümlere ayrılmıştır ve her bölüm başlığı numaralıdır:

| Bölüm | İçerik |
|---|---|
| 1 | Anahtarlar |
| 2 | Tasarım token'ları (renk, gradient, gölge, yarıçap, boşluk, blur, hareket, tipografi) |
| 3 | Fonksiyonlar ve mixin'ler (`motion`, `glass`, `gradient-outline`, `focus-ring`) |
| 4 | Kök katman: çalışma zamanı değişkenleri, yerel YTM/Polymer değişken geçersiz kılmaları, aurora |
| 5 | Keyframes |
| 6 | Global: canvas, seçim rengi, scrollbar, focus |
| 7 | Header: üst bar, logo, arama, avatar |
| 8 | Sidebar |
| 9 | Ana sayfa: raflar, başlıklar, chip'ler, carousel okları |
| 10 | Kartlar |
| 11 | Şarkı listeleri |
| 12 | Alt oynatıcı |
| 13 | Oynatıcı sayfası ve kuyruk |
| 14 | Menüler, diyaloglar, tooltip'ler, modal arka planı |
| 15 | Butonlar, ikonlar, form kontrolleri |
| 16 | Responsive |
| 17 | Azaltılmış hareket (`prefers-reduced-motion`) |

Kod içindeki yorumlar her kuralın doğrulanmış mı yoksa varsayım mı olduğunu işaretler (aşağıya bakın).

---

## Seçici durumu: doğrulanmış ve varsayım

Dosyadaki yorumlarda üç etiket kullanılır:

- **`[V]` doğrulanmış:** Seçici veya değişken, temel alınan varsayılan SCSS dosyasında bulunuyor.
- **`[A]` varsayım:** Bilinen YouTube Music / Polymer yapısına dayanıyor ama varsayılan dosyada yok. YouTube Music arayüzü değişmiş olabilir.
- **`[D]` tasarım:** DOM'a bağlı olmayan tema değişkeni veya kancası.

**Doğrulanmış (`[V]`) başlıca seçiciler:** `#progress-bar.ytmusic-player-bar`, `#sliderContainer`, `ytmusic-player-bar`, `.image.ytmusic-player-bar`, `.play-pause-button.ytmusic-player-bar`, `.spinner-container.ytmusic-player-bar`, `#right-controls`, `.middle-controls-buttons`, `ytmusic-player-queue`, `ytmusic-player-queue-item`, `ytmusic-player-page`, `ytmusic-tab-renderer.ytmusic-player-page`, `ytmusic-browse-response`, `ytmusic-two-row-item-renderer`, `.thumbnail-overlay`, `.ytmusic-play-button-renderer`, `.logo.ytmusic-nav-bar`, `#player-bar-background.ytmusic-app-layout`, `tp-yt-paper-dialog`, `tp-yt-paper-listbox.ytmusic-menu-popup-renderer`.

**Varsayım (`[A]`) olanlar ve en belirsizleri:**

- `#nav-bar-background.ytmusic-app-layout` (üst bar camı)
- `ytmusic-app-layout` ve `#content.ytmusic-app-layout` şeffaflığı (aurora'nın görünmesi buna bağlı)
- Sidebar: `tp-yt-app-drawer#guide`, `ytmusic-guide-entry-renderer` ve `[active]` durumu
- Arama: `ytmusic-search-box`, `#input-box`, öneri bileşenleri
- Chip'ler: `ytmusic-chip-cloud-chip-renderer` ve `[is-selected]`
- Şarkı satırı: `ytmusic-responsive-list-item-renderer` ve çalma durumu için `[play-button-state]`
- `ytmusic-thumbnail-renderer[thumbnail-crop=…]` (artist kartlarını daire tutmak için)
- Slider iç elemanları: `#primaryProgress`, `.slider-knob-inner`
- `--ytmusic-*`, `--yt-spec-*` ve `--paper-*` değişken adları
- `.yt-spec-button-shape-next*` buton sınıfları

---

## Performans ve erişilebilirlik

- `backdrop-filter` yalnızca altından içerik kayan yüzeylerde kullanılır: üst bar, alt oynatıcı, menüler, diyaloglar, tooltip'ler ve arama önerileri. Modal arka planı yalnızca modal açıkken bulanıklaşır.
- Desteklenmediğinde ya da `$enable-glass-blur: false` olduğunda tüm bu yüzeyler opak yedek renge düşer.
- Pencere 1100 px'in altına inince blur yarıçapları küçülür.
- Aurora yalnızca gradient'lerden oluşur (blur filtresi yok) ve `transform` ile hareket eder; render maliyeti düşüktür. Yine de sürekli animasyon istemiyorsanız `$enable-ambient-motion: false` yapın.
- `prefers-reduced-motion: reduce` etkinse geçiş süreleri sıfırlanır, aurora kayması ve giriş animasyonları kapanır. Uygulamanın kendi animasyonlarına dokunulmaz (birçoğu `transitionend` olayına bağlıdır).
- Metin kontrastı: ikincil metin yaklaşık 6.5:1, üçüncül metin yaklaşık 4.6:1 (`$bg-1` üzerinde).
- Klavye focus durumu görünür kalır; hiçbir etkileşimli alan gizlenmez.
- Dokunmatik cihazlarda (`hover: none`) kapak zoom'u kapanır.

---

## Sorun giderme

| Sorun | Olası neden ve çözüm |
|---|---|
| Hiçbir şey değişmedi | CSS yüklenmemiş ya da önbellekte. Sayfayı sert yenileyin; CSS'in `music.youtube.com` / uygulama için etkin olduğunu kontrol edin. |
| Koyu renkler geldi ama aurora görünmüyor | `ytmusic-app-layout` arka planı şeffaflaşmamış olabilir. DevTools'ta layout elemanının `background` değerine bakın; `$enable-ambient: false` ile devre dışı da bırakabilirsiniz. |
| Üst bar camı görünmüyor | `#nav-bar-background` seçicisi `[A]` varsayımıdır. DevTools'ta gerçek elemanı bulup seçiciyi güncelleyin. |
| Sidebar'da aktif öğe vurgulanmıyor | Aktif durum için `[active]` ve `.iron-selected` denenir; uygulamanız farklı bir öznitelik kullanıyorsa §8'deki seçiciyi uyarlayın. |
| Çalan şarkı satırı vurgulanmıyor | `[play-button-state]` öznitelik adları varsayımdır; §11'i gerçek özniteliğe göre düzenleyin. |
| Yazı tipi değişmedi | Tema yalnızca bilgisayarınızda kurulu fontları kullanır (`Inter` varsa o, yoksa `Roboto`). Uzak font yüklenmez. |
| Dar pencerede oynatıcı paneli bozuk | Varsayılan dosyadan gelen 968 px altı kuralı (§13) korunmuştur; `#main-panel` kenar boşluğunu oradan ayarlayın. |
| Blur performansı düşürüyor | `$enable-glass-blur: false` ile derleyin ya da `--ytmx-blur-*` değerlerini küçültün. |

---

## Varsayılan dosyadan korunan ve düzeltilen noktalar

**Korunanlar:** ilerleme çubuğunu tam genişliğe çeken kural, `ytmusic-browse-response` görünürlük düzeltmesi, kuyruk scrollbar gizleme hilesi, oynatıcı ikon ve buton boyutları, spinner boyutu, 968 px altı oynatıcı paneli düzeni.

**Düzeltilenler:**

- `--ytmusic-player-bar-background: $playerBarBackground` ve `--yt-icon-button-icon-height: $playerIconSize - 4px` satırları, Dart Sass custom property içindeki `$değişken`leri işlemediği için çıktıya ham metin olarak yazılıyordu ve etkisizdi. Artık `#{}` ile yazılmıştır (çıktıda `#0b0914` ve `16px`).
- Artist (daire) kartlarının overlay'i kare kalıyordu; `:has()` ile daire yapıldı.
- Yorum satırındaki kişisel arka plan görseli kuralı kaldırıldı.
- Genel kart seçicisi (`ytmusic-two-row-item-renderer`), yalnızca carousel'dekileri değil, Keşfet / Kütüphane gibi ızgaralardaki kartları da kapsayacak şekilde genişletildi.

---

## Bilinen sınırlar

- Carousel kapsayıcısı taşan içeriği kırptığı için kartlar yukarı yükselmez; hover'da kapak zoom'u, overlay ve oynatma butonu efekti kullanılır.
- Albüm / playlist başlık alanında yalnızca kapak görseli ve gölge düzenlenmiştir; başlığın tamamı yeniden tasarlanmamıştır.
- Sidebar ve kartlar blur yerine yarı saydam dolgu kullandığı için cam hissi üst bar ve oynatıcıya göre daha yumuşaktır; bu bilinçli bir performans tercihidir.
- Tema yalnızca görünümü değiştirir. Albüm kapağından renk çıkarma gibi davranış gerektiren özellikler için ayrıca JavaScript gerekir.
