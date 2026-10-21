# Smart Fields Pro – Geliştirici Rehberi

Bu rehber temanızda ve eklentilerinizde Smart Fields Pro (SFP) verisini nasıl okuyup yazacağınızı, ACF'den nasıl geçeceğinizi ve hazır örnek tema kodlarını anlatır. Kararlı (sürümler arasında değişmeyen) yüzey: `sfp_*` fonksiyonları, `sfp_api()`, REST rotaları, kancalar ve CSS değişkenleri. `SFP_*` iç sınıfları değişebilir.

## Başlarken

Eklentinin etkin olduğunu kontrol edin; tema SFP olmadan da hata vermesin:

```php
if ( function_exists( 'sfp_get_field' ) ) {
	echo esc_html( sfp_get_field( 'price', null, 'plain' ) );
}
```

- **Alan anahtarı**: Alan Grupları ekranında her alanın altında görünen koddur (ör. `price`, `net_area`).
- **Veritabanı**: Yazı alanları `_sfp_{anahtar}` meta anahtarıyla saklanır (ör. `_sfp_price`). Kullanıcı alanları da aynı anahtarla kullanıcı meta'sında durur.
- **Yazının şablonu**: Yazı düzenleyicide seçilen grup `_sfp_group` meta'sında tutulur.

## Değer okuma

| Fonksiyon | Ne döndürür |
| --- | --- |
| `sfp_get_field( $key, $post_id = null, $format = 'raw' )` | `raw`: saklanan değer, `plain`: düz metin, `html`: biçimlenmiş HTML |
| `sfp_the_field( $key, $post_id = null )` | Biçimlenmiş HTML'i yazdırır |
| `sfp_get_fields( $post_id = null )` | Yazının tüm alanları: `anahtar => ham değer` |
| `sfp_fields_box( $post_id = null, $args = array() )` | Hazır alan kutusunu yazdırır |
| `sfp_get_field_object( $key, $post_id = null )` | Alan tanımı + `value`, `group`, `meta_key`, `empty` |

```php
// Ham değer (hesaplama için)
$price = (float) sfp_get_field( 'price' );

// Biçimlenmiş (para birimi, birim, bağlantı, yıldız vb.)
sfp_the_field( 'price' );

// Düz metin (başlık, meta etiketi, aria-label için)
$rooms = sfp_get_field( 'rooms', null, 'plain' );

// Alan bilgisi: etiket, tip, seçenekler
$f = sfp_get_field_object( 'property_type' );
if ( $f && ! $f['empty'] ) {
	printf( '<strong>%s:</strong> %s', esc_html( $f['label'] ), esc_html( sfp_get_field( 'property_type', null, 'plain' ) ) );
}
```

`sfp_fields_box()` argümanları: `fields` (virgüllü anahtarlar), `exclude`, `layout` (`cards`, `table`, `list`, `minimal`), `title`, `show_title`, `class`.

```php
sfp_fields_box( get_the_ID(), array( 'fields' => 'price,rooms,net_area', 'layout' => 'table', 'title' => 'Özet' ) );
```

## Değer yazma ve silme

| Fonksiyon | Açıklama |
| --- | --- |
| `sfp_update_field( $key, $value, $post_id = null )` | Değeri düzenleyicideki gibi temizleyip kaydeder; boş değer meta'yı siler. `true` / `false` |
| `sfp_delete_field( $key, $post_id = null )` | Değeri ve yardımcı değerlerini (para birimi, harita, varyasyon) siler |

```php
sfp_update_field( 'price', 2450000, $post_id );
sfp_update_field( 'features', array( 'Asansör', 'Otopark' ), $post_id );   // checkbox: seçenek değerleri
sfp_update_field( 'team', array(                                            // repeater: satırlar
	array( 'name' => 'Ada', 'role' => 'Tasarım' ),
	array( 'name' => 'Can', 'role' => 'Yazılım' ),
), $post_id );
sfp_delete_field( 'old_price', $post_id );
```

Kaydedilen her alan için `sfp_field_saved` aksiyonu çalışır.

## Satır döngüleri (repeater, esnek içerik, grup, listeler)

ACF'deki `have_rows()` alışkanlığıyla aynıdır. Desteklenen alanlar: **Repeater**, **Flexible Content**, **Group**, **Tekrarlanabilir Liste**, **Anahtar–Değer Tablosu**, **Gruplu Liste** ve **Gruplu Anahtar–Değer**.

| Fonksiyon | Açıklama |
| --- | --- |
| `sfp_have_rows( $key, $post_id = null )` | Sıradaki satır var mı? Satır içindeyken alt alanın satırlarını dolaşır (iç içe) |
| `sfp_the_row()` | Sonraki satıra geçer, satırı döndürür |
| `sfp_get_sub_field( $key, $format = 'raw' )` / `sfp_the_sub_field( $key )` | Satırdaki alt alan |
| `sfp_get_row()` / `sfp_get_row_index()` | Satırın tamamı / 1'den başlayan sıra |
| `sfp_get_row_layout()` | Esnek içerikte satırın düzen anahtarı |
| `sfp_reset_rows()` | `break` ile erken çıktığınız döngüleri kapatır |

```php
<?php if ( sfp_have_rows( 'team' ) ) : ?>
	<ul class="team">
	<?php while ( sfp_have_rows( 'team' ) ) : sfp_the_row(); ?>
		<li>
			<b><?php echo esc_html( sfp_get_sub_field( 'name' ) ); ?></b>
			<?php sfp_the_sub_field( 'photo' ); // Biçimlenmiş görsel ?>
		</li>
	<?php endwhile; ?>
	</ul>
<?php endif; ?>
```

Liste ve tablo alanlarında alt alan anahtarları:

| Alan tipi | Alt alanlar |
| --- | --- |
| Tekrarlanabilir Liste | `value` |
| Anahtar–Değer Tablosu | `k`, `v` |
| Gruplu Liste | `title`, iç döngü `items` (her öğe `value`) |
| Gruplu Anahtar–Değer | `title`, iç döngü `rows` (`k`, `v`) |

```php
// Gruplu anahtar–değer: "Teknik Özellikler" tablosu
while ( sfp_have_rows( 'specs' ) ) : sfp_the_row();
	echo '<h4>' . esc_html( sfp_get_sub_field( 'title' ) ) . '</h4><dl>';
	while ( sfp_have_rows( 'rows' ) ) : sfp_the_row();
		echo '<dt>' . esc_html( sfp_get_sub_field( 'k' ) ) . '</dt><dd>' . esc_html( sfp_get_sub_field( 'v' ) ) . '</dd>';
	endwhile;
	echo '</dl>';
endwhile;
```

### Esnek içerik ile sayfa kurucu

```php
while ( sfp_have_rows( 'sections' ) ) : sfp_the_row();
	switch ( sfp_get_row_layout() ) {
		case 'hero':
			printf( '<section class="hero"><h2>%s</h2>%s</section>', esc_html( sfp_get_sub_field( 'title' ) ), wp_kses_post( sfp_get_sub_field( 'text', 'html' ) ) );
			break;
		case 'quote':
			printf( '<blockquote>%s</blockquote>', esc_html( sfp_get_sub_field( 'quote' ) ) );
			break;
	}
endwhile;
```

## Alan tiplerine göre ham değerler

| Tip | Ham değer (`raw`) |
| --- | --- |
| Metin, e-posta, telefon, URL, video | `string` |
| Sayı, fiyat, puan, aralık | Sayısal `string` (fiyatın para birimi ayrıca `_sfp_{anahtar}__cur`) |
| Evet/Hayır | `'1'` veya `'0'` |
| Seçim, radyo | Seçilen değer `string` |
| Onay kutuları, çoklu seçim | Değer dizisi |
| Tarih / Saat / Tarih-saat | `Y-m-d` / `H:i` / `Y-m-d\TH:i` |
| Süre | Dakika `string` |
| Görsel, dosya | Ek (attachment) ID `string` → `wp_get_attachment_image( $id, 'large' )` |
| Galeri | Ek ID dizisi |
| Yazı nesnesi, ilişki, taksonomi, kullanıcı | ID dizisi (tek seçimde de dizi olabilir) |
| Bağlantı | `array( 'url', 'title', 'target' )` |
| Grup / Repeater / Esnek | Satır dizisi (esnekte her satırda `_l` düzen anahtarı) |

```php
// Görsel
$img = (int) sfp_get_field( 'cover' );
echo $img ? wp_get_attachment_image( $img, 'large', false, array( 'loading' => 'lazy' ) ) : '';

// İlişki: ilgili yazılar
foreach ( (array) sfp_get_field( 'related' ) as $rid ) {
	printf( '<a href="%s">%s</a>', esc_url( get_permalink( $rid ) ), esc_html( get_the_title( $rid ) ) );
}
```

## Örnek tema kodları

### Tekil sayfa: emlak ilanı (single.php)

```php
<?php get_header(); ?>
<main class="listing">
<?php while ( have_posts() ) : the_post(); ?>
	<h1><?php the_title(); ?></h1>
	<p class="price"><?php sfp_the_field( 'price' ); ?></p>
	<ul class="facts">
		<?php foreach ( array( 'rooms', 'net_area', 'floor', 'heating' ) as $k ) :
			$f = sfp_get_field_object( $k );
			if ( ! $f || $f['empty'] ) { continue; } ?>
			<li><span><?php echo esc_html( $f['label'] ); ?></span> <b><?php sfp_the_field( $k ); ?></b></li>
		<?php endforeach; ?>
	</ul>
	<?php the_content(); ?>
	<?php sfp_fields_box( get_the_ID(), array( 'exclude' => 'price,rooms,net_area,floor,heating' ) ); ?>
<?php endwhile; ?>
</main>
<?php get_footer();
```

### Arşiv kartı (archive.php / döngü)

```php
<article class="card">
	<a href="<?php the_permalink(); ?>"><?php the_post_thumbnail( 'medium' ); ?></a>
	<h2><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>
	<p><?php echo esc_html( sfp_get_field( 'rooms', null, 'plain' ) ); ?> · <?php echo esc_html( sfp_get_field( 'net_area', null, 'plain' ) ); ?></p>
	<strong><?php sfp_the_field( 'price' ); ?></strong>
</article>
```

### Alan değerine göre sorgu (WP_Query)

```php
$q = new WP_Query( array(
	'post_type'  => 'post',
	'meta_query' => array(
		array( 'key' => '_sfp_price', 'value' => array( 1000000, 3000000 ), 'type' => 'NUMERIC', 'compare' => 'BETWEEN' ),
		array( 'key' => '_sfp_rooms', 'value' => '3+1' ),
	),
	'meta_key'   => '_sfp_price',
	'orderby'    => 'meta_value_num',
	'order'      => 'ASC',
) );
```

Onay kutusu gibi dizi değerler serileştirilmiş saklanır; tek değer aramak için `'compare' => 'LIKE'` ve `'value' => '"Otopark"'` kullanın.

### Kullanıcı alanları (profil)

```php
$city = sfp_api()->user_get( get_current_user_id(), 'location' );
echo sfp_api()->display( 'GRUP_ID', 'user', $user_id ); // Profil kartları
```

### Ön yüz formu (üyeler için)

```php
sfp_api()->enqueue_form();
echo '<form method="post">' . wp_nonce_field( 'my_save', '_n', true, false )
	. sfp_api()->form( array( 'g123' ), array( 'prefix' => 'my_fields', 'id' => $post_id ) )
	. '<button>Kaydet</button></form>';

// Gönderimde:
if ( isset( $_POST['_n'] ) && wp_verify_nonce( $_POST['_n'], 'my_save' ) && current_user_can( 'edit_post', $post_id ) ) {
	$gid    = sanitize_key( $_POST['sfp_group'] );
	$result = sfp_api()->save( 'post', $post_id, $gid, wp_unslash( $_POST['my_fields'][ $gid ] ) );
	if ( is_wp_error( $result ) ) {
		echo esc_html( $result->get_error_message() );
	}
}
```

## ACF'den geçiş

| ACF | Smart Fields Pro |
| --- | --- |
| `get_field( 'x' )` | `sfp_get_field( 'x' )` |
| `the_field( 'x' )` | `sfp_the_field( 'x' )` |
| `get_fields()` | `sfp_get_fields()` |
| `get_field_object( 'x' )` | `sfp_get_field_object( 'x' )` |
| `update_field( 'x', $v, $id )` | `sfp_update_field( 'x', $v, $id )` |
| `delete_field( 'x', $id )` | `sfp_delete_field( 'x', $id )` |
| `have_rows()` / `the_row()` | `sfp_have_rows()` / `sfp_the_row()` |
| `get_sub_field()` / `the_sub_field()` | `sfp_get_sub_field()` / `sfp_the_sub_field()` |
| `get_row_layout()` / `get_row_index()` | `sfp_get_row_layout()` / `sfp_get_row_index()` |
| `acf_add_local_field_group()` | `sfp_add_local_group()` |
| Local JSON (`acf-json`) | Tema içinde `sfp-json` klasörü |

Kısa geçiş için temanızda geçici bir uyum katmanı kurabilirsiniz:

```php
if ( ! function_exists( 'get_field' ) && function_exists( 'sfp_get_field' ) ) {
	function get_field( $key, $post_id = null ) { return sfp_get_field( $key, $post_id ); }
	function the_field( $key, $post_id = null ) { sfp_the_field( $key, $post_id ); }
}
```

## Grupları kodla tanımlama ve JSON senkronu

### Kodla grup

Sabit bir `id` verin; dizi değiştiğinde grup yönetim panelinde bir kez güncellenir.

```php
add_action( 'init', function () {
	if ( ! function_exists( 'sfp_add_local_group' ) ) {
		return;
	}
	sfp_add_local_group( array(
		'id'         => 'gthemehero',
		'title'      => 'Sayfa Hero',
		'post_types' => array( 'page' ),
		'active'     => 1,
		'fields'     => array(
			array( 'key' => 'hero_title', 'label' => 'Başlık', 'type' => 'text', 'width' => 'full' ),
			array( 'key' => 'hero_image', 'label' => 'Görsel', 'type' => 'image' ),
			array( 'key' => 'hero_link', 'label' => 'Buton', 'type' => 'link' ),
		),
	) );
} );
```

### JSON dosyaları (git ile sürüm takibi)

1. Temanızda `sfp-json` klasörü açın (`wp-content/themes/temaniz/sfp-json`).
2. Bir grubu kaydettiğinizde `{id}.json` olarak oraya yazılır.
3. Dosyalar başka bir sitede daha yeniyse yönetim paneline girildiğinde otomatik içe alınır.

```php
// Farklı klasör:
add_filter( 'sfp_json_save_path', fn() => WP_CONTENT_DIR . '/sfp-json' );
add_filter( 'sfp_json_load_paths', fn( $paths ) => array_merge( $paths, array( get_template_directory() . '/sfp-json' ) ) );
```

## sfp_api() metotları

| Metot | Döndürür |
| --- | --- |
| `version()` | Eklenti sürümü |
| `modules()` | Etkin modüller: `shop`, `woocommerce`, `appointments`, `reviews`, `social`, `forms`, `video`, `ads`, `currency`, `search` |
| `pages()` | Sepet, ödeme, sipariş, randevu ve hesap sayfası adresleri |
| `groups( array( 'post_type' => 'post', 'kind' => 'shop' ) )` | Etkin gruplar |
| `group( $id )` / `fields( $group_or_id )` | Tek grup / alan listesi (ziyaretçi diline çevrilmiş) |
| `get( $key, $post_id, $format )` / `get_all( $post_id, 'plain' )` | Tek değer / görünür tüm değerler |
| `user_get( $user_id, $key )` | Kullanıcı alanının ham değeri |
| `save( 'post' \| 'user', $id, $group_id, $values, $args )` | `true` veya `WP_Error` (zorunlu alan boşsa hiçbir şey yazılmaz) |
| `enqueue_form()` / `form( $groups, $args )` | Ön yüz alan düzenleyicisi |
| `display( $group_id, 'user', $user_id )` | Kullanıcı profil kartları |
| `account_tabs( $user_id )` | Siparişler, adres ve randevu sekmeleri |
| `member_template()` | "Üye Profili" grubunun ID'si |

```php
$modules = sfp_api()->modules();
if ( ! empty( $modules['shop'] ) ) {
	echo '<a href="' . esc_url( sfp_api()->pages()['cart'] ?? '' ) . '">Sepet</a>';
}
```

## REST API

Taban adres: `/wp-json/sfp/v1`

| Rota | Not |
| --- | --- |
| `GET /groups?post_type=post` | Herkese açık |
| `GET /posts/{id}?format=plain` | Yayınlanmış (veya okuyabildiğiniz) yazılar |
| `POST /posts/{id}` | `{"group":"g123","fields":{"price":"99"}}` — `edit_post` yetkisi ve `X-WP-Nonce` başlığı gerekir |
| `GET /users/{id}/profile` | Herkese açık profil alanları (Smart Social Pro) |

```js
fetch( '/wp-json/sfp/v1/posts/42?format=plain' )
	.then( ( r ) => r.json() )
	.then( ( d ) => console.log( d.fields.price ) );
```

## JavaScript

```js
SFPFront.rest( 'posts/42' ).then( ( d ) => console.log( d.fields ) );
SFPFront.rest( 'posts/42', { body: { fields: { price: '120' } } } ); // Giriş yapmış kullanıcılar
document.addEventListener( 'sfp:cart', ( e ) => console.log( e.detail.count ) );
SFPFront.lightbox( [ { type: 'image', src: '/buyuk.jpg', thumb: '/kucuk.jpg' } ], 0, 'Başlık' );
```

## Kancalar

**Aksiyonlar:** `sfp_field_saved`, `sfp_saved_post`, `sfp_group_saved`, `sfp_api_saved`, `sfp_api_ready`, `sfp_order_created`, `sfp_order_status`, `sfp_order_paid`, `sfp_stock_changed`, `sfp_appt_created`, `sfp_appt_status`, `sfp_form_submitted`.

**Filtreler:** `sfp_field_types`, `sfp_templates`, `sfp_schema_data`, `sfp_format_value`, `sfp_plain_value`, `sfp_render_box`, `sfp_account_tabs`, `sfp_account_url`, `sfp_profile_group`, `sfp_carriers`, `sfp_search_query_args`, `sfp_menu_location`, `sfp_json_save_path`, `sfp_json_load_paths`.

```php
// JSON-LD'ye alan ekleme
add_filter( 'sfp_schema_data', function ( $data, $post_id ) {
	$data['award'] = sfp_get_field( 'award', $post_id, 'plain' );
	return $data;
}, 10, 2 );

// Bir alanın görünümünü değiştirme
add_filter( 'sfp_format_value', function ( $html, $field, $value, $post_id ) {
	return 'price' === $field['key'] ? '<mark>' . $html . '</mark>' : $html;
}, 10, 4 );

// Sipariş ödendiğinde
add_action( 'sfp_order_paid', function ( $order_id ) {
	error_log( 'Ödendi: ' . $order_id );
} );
```

Bu aksiyonlar WordPress kancalarını dinleyen otomasyon araçlarında (Uncanny Automator, WP Webhooks vb.) tetikleyici olarak da kullanılabilir.

## Kısa kodlar ve bloklar

`[sfp_fields]`, `[sfp_field key=""]`, `[sfp_gallery]`, `[sfp_search]`, `[sfp_form id=""]`, `[sfp_reviews]`, `[sfp_rating]`, `[sfp_reviews_page]`, `[sfp_currency]`, `[sfp_cart]`, `[sfp_checkout]`, `[sfp_order]`, `[sfp_account]`, `[sfp_booking]`, `[sfp_appointment]`, `[sfp_seller]`.

Bloklar: **Smart Fields** ve **Smart Fields Search**. Elementor dinamik etiketleri, Bricks `{sfp_key}`, Yoast `%%sfp_key%%`, Rank Math `%sfp_key%`, Gravity Forms / WPForms `{sfp:key}`, Fluent Forms `{sfp_key}`.

## Modüller

Ayarlar → Modüller ekranında kapatılan modül sitede hiç kod yüklemez. Temada kontrol için:

```php
if ( class_exists( 'SFP_Modules' ) && SFP_Modules::on( 'reviews' ) ) {
	echo do_shortcode( '[sfp_rating]' );
}
```

Kapalı modülün kısa kodları boş çıktı verir, sayfada ham `[kısa_kod]` görünmez.

## CSS değişkenleri

```css
.sfp {
	--sfp-accent: #4f46e5;
	--sfp-radius: 14px;
	--sfp-bg: #fff;
	--sfp-soft: #f8fafc;
	--sfp-line: #e5e7eb;
	--sfp-ink: #0f172a;
	--sfp-mute: #526077;
}
```
