<p align="center">
  <img src="assets/banner-tr.png" alt="Smart Fields Pro – WordPress için sektöre hazır özel alanlar" width="100%">
</p>

<h1 align="center">Smart Fields Pro</h1>

<p align="center">
  <b>WordPress için sektöre hazır özel alan şablonları</b><br>
  Şablonu seç, alanlar açılsın. Schema.org JSON-LD, puan ve yorum sistemi, formlar, çoklu para birimi ve WooCommerce desteği hazır.
</p>

<p align="center">
  <img src="https://img.shields.io/github/v/release/AppsTRDesign/Smart-Fields-Pro">
  <img src="https://img.shields.io/github/downloads/AppsTRDesign/Smart-Fields-Pro/total">
  <img src="https://img.shields.io/badge/WordPress-6.0%2B-21759b?logo=wordpress" alt="WordPress 6.0+">
  <img src="https://img.shields.io/badge/PHP-7.4%2B-777bb4?logo=php&amp;logoColor=white" alt="PHP 7.4+">
  <img src="https://img.shields.io/badge/WooCommerce-HPOS%20uyumlu-96588a?logo=woocommerce&amp;logoColor=white" alt="WooCommerce HPOS uyumlu">
  <img src="https://img.shields.io/badge/lisans-GPLv2%2B-16a34a" alt="GPLv2 veya üstü">
</p>

<p align="center">
  <a href="https://wplugins.shop/eklenti/smart-fields-pro.zip"><b>⬇ İndir</b></a> ·
  <a href="#özellikler">Özellikler</a> ·
  <a href="#kısa-kodlar">Kısa Kodlar</a> ·
  <a href="#php-api">PHP API</a> ·
  <a href="README.md">🇬🇧 English</a>
</p>

---

## Neden Smart Fields Pro?

Klasik özel alan eklentileri size boş bir sayfa verir. Smart Fields Pro ise **gerçek sektörler için hazırlanmış alan setleri** sunar. Yazıyı açıp **Emlak** şablonunu seçtiğinizde fiyat, metrekare, oda sayısı, kat, ısınma ve konum alanları gelir. **Tarif** seçtiğinizde malzemeler, resimli adımlar, süreler ve porsiyon hazırdır. Her alan **schema.org JSON-LD** verisine bağlıdır, aramaya dahildir ve mobil uyumludur.

Kod yazan geliştiriciler için de tanıdık bir API var: `sfp_get_field()`, `sfp_have_rows()`, `sfp_update_field()`… ACF'den geçiş tablosu ve tema örnekleri yönetim panelindeki **Geliştirici rehberinde**.

## 1.4.1'deki yenilikler

- 🧩 **Modüller:** Dükkan, Randevular, Formlar, Yorumlar, Video oynatıcı, Reklamlar, Para birimleri, Arama ve WooCommerce entegrasyonu ayrı ayrı kapatılabilir. Kapalı modül **hiç kod yüklemez**, verisi korunur.
- 👩‍💻 **ACF tarzı API:** `sfp_get_field_object()`, `sfp_update_field()`, `sfp_delete_field()`, `sfp_have_rows()` / `sfp_the_row()` / `sfp_get_sub_field()` / `sfp_get_row_layout()` (iç içe döngüler dahil).
- 🗂 **Kodla grup ve JSON senkronu:** `sfp_add_local_group()` ve temadaki `sfp-json` klasörü (git dostu, ACF Local JSON gibi).
- 📘 **Panel içinde geliştirici rehberi:** renkli kod, içindekiler, arama ve kopyalama düğmeleri; Türkçe ve İngilizce.
- 💾 **Tek Kaydet düğmesi** ve "Kaydedilmemiş değişiklikler" uyarısı; Siparişler / Randevular sekmeleri anahtarla anında açılıp kapanır.
- 🧹 Dükkan veya randevu kapatılınca sayfaları ve menü öğeleri kaldırılır, açılınca geri gelir. Silinen reklamın istatistikleri de silinir.
- 🎯 Ön yüz kutusu **şablon sırasını birebir** izler; Smart Social Pro formları düzenleyiciyle aynı düzende.
- ✅ Google için `uploadDate` saat dilimiyle; erişilebilirlik (başlık sırası, kontrast, liste yapısı) düzeltmeleri.

## Özellikler

### 🧩 Şablonlar ve alanlar
- **22 hazır şablon:** Emlak, Yemek Tarifi, Ürün, Film, Dizi, Etkinlik, İş İlanı, Vasıta, Kitap, Otel, Restoran, Kurs, Yazılım, WordPress Eklenti / Tema, Program İndirme, Video Oyunu, Müzik Albümü, Yerel İşletme, Güzellik Merkezi / Spa, Kuaför / Berber, Ürün İncelemesi ve Doktor / Klinik. Ayrıca Boş Grup.
- **60 alan türü** ACF tarzı seçicide: fiyat (çoklu para birimi), puanlar, adres + harita, resimli adımlar, medya galerisi, sayaçlı indirme, **tekrarlayıcı, grup, esnek içerik, ilişki, taksonomi, kullanıcı, bağlantı, oEmbed, ikon seçici, sekme, akordeon** ve daha fazlası.
- Tüm şablonlarda **zengin metinli Kısa Açıklama**. İçerik yazmasanız bile özet, arama metni, WooCommerce kısa açıklaması ve JSON-LD açıklaması buradan alınır.
- İkon, genişlik, birim, varsayılan değer, seçenekler ve zorunlu alan ayarlarıyla **sürükle-bırak alan oluşturucu**. Her alan için arama, filtre ve kart anahtarları vardır.
- **Gutenberg ve Klasik Editör** ile çalışır. Zorunlu alanlar doldurulmadan yayınlanamaz.

### ⭐ Puan ve yorumlar
- Tek tıkla **ziyaretçi yıldız puanı** ve özet kartı: solda dağılım çubukları, sağda büyük ortalama puan.
- Blok ve klasik temalarda temanın yorumlarının yerini alan **gelişmiş yorum bölümü**. Kendi form tasarımcısı, **fotoğraflı değerlendirme**, beğeni, öne çıkan site sahibi yanıtı, AJAX sıralama ve sayfalama içerir.
- **Yalnızca üyeler yorum yazsın / beğensin** anahtarları. Fotoğraflı değerlendirme yalnızca üyelere veya herkese açılabilir.
- Otomatik oluşan **site geneli Yorumlar sayfası**. Şablon, yıldız ve fotoğraf filtreleri ile en yüksek puanlılar listesi vardır.
- Her şey JSON-LD'ye aktarılır: `aggregateRating`, `Review` (200 yoruma kadar) ve kriter puanları için `reviewAspect`.

### 📨 Formlar ve gelen kutusu
- **İletişim, teklif, randevu ve anket** hazır şablonlarıyla form oluşturucu.
- E-posta bildirimi ve isteğe bağlı otomatik yanıt, **WordPress maili veya SMTP** ile gönderilir. Kurulumu kontrol etmek için test maili butonu vardır.
- Mesajın hangi formdan geldiğini gösteren **admin gelen kutusu**. Okuma, yanıtlama, silme ve toplu işlemler; AJAX sayfalama ve okunmamış rozeti.
- Captcha gerektirmeyen spam koruması: bal küpü, imzalı zaman damgası ve hız sınırı.

### 🖼 Medya
- Tüm şablonlarda opsiyonel **medya galerisi**: yükleme + YouTube (Shorts dahil), TikTok, Instagram gönderi ve Reels, Vimeo, Facebook, Dailymotion, Twitch, Spotify ve SoundCloud.
- **Instagram tarzı slayt:** kaydırma, aşağı çekerek kapatma, iki parmak ve çift dokunuşla yakınlaştırma. Videolar slaytın içinde oynar.
- **Otomatik öne çıkan görsel:** öne çıkan görsel yoksa galerinin ilk resmi otomatik atanır.

### 🔎 Arama, para ve SEO
- Alan değerleri (ve WooCommerce SKU'ları) WordPress aramasına, aramanın yerini almadan eklenir.
- Filtreli, etiketli, ızgara veya liste görünümlü **AJAX arama bölümü**. Mobilde alttan açılan filtre paneli, sayfa numaraları veya "daha fazla yükle". İsteğe bağlı **arama sayfası** temanın `?s=` sonuçlarının yerini alır.
- **Çoklu para birimli fiyatlar:** her içerik kendi para birimini seçer; kurlar elle girilir veya TCMB'den otomatik güncellenir; ziyaretçiler için para birimi seçici vardır. Filtreler ve sıralama fiyatları ana para biriminde karşılaştırır.
- **Otomatik schema.org JSON-LD:** Product, Accommodation, Recipe (resimli HowToStep), Movie, TVSeries, Event, JobPosting, Car, Book, Hotel, Restaurant, Course, SoftwareApplication, VideoGame, MusicAlbum, LocalBusiness, Review ve Physician.

### 🛒 WooCommerce ve yönetim paneli
- Alanlar ürün sekmesinde veya özette görünür. Schema, tekrar oluşmadan WooCommerce Product verisiyle birleşir. **HPOS uyumlu.**
- Her işlemin AJAX ile yapıldığı, özel modal ve bildirimli modern tek sayfa yönetim paneli. Mobilde de eksiksiz çalışır.
- Ön yüz stilleri izole edilmiştir: tema bozamaz, stiller de temaya taşmaz.
- Çoklu dil desteği: İngilizce kaynak `.pot` ve eksiksiz Türkçe çeviri.

<p align="center"><img src="assets/square-tr.png" alt="Smart Fields Pro özellikleri" width="540"></p>

## Kurulum

1. [Zip dosyasını indirin](https://wplugins.shop/eklenti/smart-fields-pro.zip).
2. WordPress'te **Eklentiler → Yeni Ekle → Eklenti Yükle** yolunu izleyin, zip'i yükleyip etkinleştirin.
3. **Smart Fields → Şablon Galerisi**'ni açın, bir şablon seçin ve hangi içerik türlerinde kullanılacağını belirleyin.
4. Bir yazıyı düzenleyin, **Smart Fields** kutusundan şablonu seçin, alanları doldurup yayınlayın.

İsteğe bağlı: **Smart Fields → Ayarlar** altından **Yorumlar ve Puanlar**, **Mail/SMTP**, **Para Birimleri** ve **Arama sayfası**'nı açın.

## Kısa Kodlar

| Kısa kod | Açıklama |
|---|---|
| `[sfp_fields]` | İçeriğin tüm alanları (`fields`, `exclude`, `layout`, `title`, `id`) |
| `[sfp_field key="price"]` | Tek bir alanın biçimlendirilmiş değeri |
| `[sfp_gallery]` | İçeriğin medya galerisi |
| `[sfp_search group="GRUP_ID"]` | Filtrelenebilir AJAX arama bölümü (`per_page`, `columns`, `pagination`, `view`) |
| `[sfp_search]` | Tüm içerikte şablon sekmeleriyle arama |
| `[sfp_form id="FORM_ID"]` | **Smart Fields → Formlar**'da oluşturulan form |
| `[sfp_reviews]` | İçeriğin yorum bölümü |
| `[sfp_rating]` | Hızlı oylamalı puan özet kartı |
| `[sfp_reviews_page]` | Filtreli, site geneli son yorumlar |
| `[sfp_currency]` | Ziyaretçi para birimi seçici |

Gutenberg blokları: **Smart Fields** (tüm alanlar, tek alan veya galeri) ve **Smart Fields Search**.

## Geliştirici API'si

```php
// Değer okuma
$price = sfp_get_field( 'price' );            // ham
sfp_the_field( 'price' );                      // biçimlenmiş
$f = sfp_get_field_object( 'rooms' );          // etiket, tip, değer

// Yazma ve silme
sfp_update_field( 'price', 2450000, $post_id );
sfp_delete_field( 'old_price', $post_id );

// Tekrarlayıcı / esnek içerik
while ( sfp_have_rows( 'team' ) ) : sfp_the_row();
	echo esc_html( sfp_get_sub_field( 'name' ) );
endwhile;

// Kodla alan grubu (temada sfp-json klasörüyle JSON senkronu)
sfp_add_local_group( array( 'id' => 'gthemehero', 'title' => 'Hero', 'post_types' => array( 'page' ), 'active' => 1, 'fields' => array(
	array( 'key' => 'hero_title', 'label' => 'Başlık', 'type' => 'text' ),
) ) );
```

| ACF | Smart Fields Pro |
|---|---|
| `get_field()` / `the_field()` | `sfp_get_field()` / `sfp_the_field()` |
| `update_field()` / `delete_field()` | `sfp_update_field()` / `sfp_delete_field()` |
| `have_rows()` / `the_row()` / `get_sub_field()` | `sfp_have_rows()` / `sfp_the_row()` / `sfp_get_sub_field()` |
| `acf_add_local_field_group()` | `sfp_add_local_group()` |

Ayrıca: `sfp_api()` (gruplar, doğrulamalı kayıt, ön yüz formları), `/wp-json/sfp/v1` REST rotaları, `SFPFront.rest()` ve 25'ten fazla kanca. Tam rehber: **Smart Fields → Kullanım → Geliştirici rehberi** veya [`docs/developer-api-tr.md`](docs/developer-api-tr.md).

**Filtreler:** `sfp_templates`, `sfp_field_types`, `sfp_schema_data`, `sfp_render_box`, `sfp_format_value`, `sfp_search_query_args`, `sfp_schema_reviews_max`, `sfp_reviews_active`, `sfp_reviews_on_products`.

## Gereksinimler

- WordPress 6.0 veya üstü
- PHP 7.4 veya üstü
- İsteğe bağlı: WooCommerce (HPOS desteklenir)

## Değişiklik Günlüğü

### 1.4.1
- Modüller ekranı (kapalı modül kod yüklemez), ACF tarzı API (`sfp_have_rows`, `sfp_update_field`, `sfp_get_field_object`…), `sfp_add_local_group()` ve JSON senkronu, panelde renkli kodlu geliştirici rehberi.
- Tek Kaydet düğmesi, anlık sekme görünürlüğü, dükkan/randevu sayfa ve menü temizliği, silinen reklamın istatistik temizliği.
- Ön yüzde şablon sırası, Smart Social Pro formlarında editörle aynı düzen, `uploadDate` ve erişilebilirlik düzeltmeleri.

### 1.4.0
- Dükkan (sepet, ödeme, sipariş takibi, PayTR / Stripe / PayPal), randevu sistemi, film/dizi oynatıcı ve video reklamları, kargo profilleri, ülke/il listeleri, satıcı bilgileri.
- Tekrarlayıcı, grup, esnek içerik, ilişki ve daha fazlasıyla 60 alan türü; yazıya özel düzen.
- `sfp_api()`, REST rotaları, WPML / Polylang, Elementor / Bricks, Yoast / Rank Math entegrasyonları, Smart Social Pro köprüsü.

### 1.3.1

* Yeni: özet kartlı ziyaretçi puanları; tüm puan ve yorumlar JSON-LD'ye eklenir.
* Yeni: fotoğraflı yorum, beğeni, site sahibi yanıtı, üyelere özel anahtarlar, AJAX sıralama ve sayfalama içeren gelişmiş yorum bölümü.
* Yeni: filtreli ve en yüksek puanlılar listeli site geneli Yorumlar sayfası.
* Yeni: hazır şablonlu form oluşturucu, bildirimler, otomatik yanıt, WordPress maili veya SMTP ve admin gelen kutusu.
* Yeni: her adıma resim veya video eklenebilen Adımlar alanı ve Çoklu Puan alanı.
* Yeni: döviz kurlu ve ziyaretçi para birimi seçicili çoklu para birimi desteği.
* Yeni: tüm şablonlarda zengin metinli Kısa Açıklama ve galeriden otomatik öne çıkan görsel.
* İyileştirme: tüm yeni ekranlar tamamen mobil uyumlu.
* Puan ve yorum sistemi, gelen kutulu form oluşturucu, çoklu para birimi ve Kısa Açıklama alanı eklendi. Yorumlar, hızlı puan ve para birimi seçici siz açana kadar kapalı kalır.

### 1.3.0
- Ziyaretçi puanları, gelişmiş yorum bölümü (fotoğraflı yorum, beğeni, site sahibi yanıtı) ve site geneli Yorumlar sayfası. Hepsi JSON-LD'ye aktarılır.
- WordPress maili veya SMTP ile çalışan form oluşturucu ve admin gelen kutusu.
- Yeni Adımlar (resim/video) ve Çoklu Puan alanları.
- Döviz kurları ve para birimi seçicili çoklu para birimi desteği.
- Zengin metinli Kısa Açıklama ve galeriden otomatik öne çıkan görsel.

### 1.2.0
- Gruplu Liste alanı, isteğe bağlı gelişmiş arama sayfası ve tüm içerikte arama.

### 1.1.0
- Medya galerisi, Instagram tarzı slayt, İndirme alanı, eklenti ve program şablonları, yeniden tasarlanan AJAX arama.

## Lisans

GPLv2 veya üstü. Geliştirici: **AppsTRDesign** — [wplugins.shop](https://wplugins.shop)
