# Smart Fields Pro – Developer Guide

This guide shows how to read and write Smart Fields Pro (SFP) data in themes and plugins, how to move from ACF, and gives ready-to-use theme code. The stable surface (kept between versions) is: the `sfp_*` functions, `sfp_api()`, the REST routes, hooks and CSS variables. Internal `SFP_*` classes can change.

## Getting started

Check that the plugin is active so the theme also works without it:

```php
if ( function_exists( 'sfp_get_field' ) ) {
	echo esc_html( sfp_get_field( 'price', null, 'plain' ) );
}
```

- **Field key**: the code shown under each field on the Field Groups screen (for example `price`, `net_area`).
- **Database**: post fields are stored as `_sfp_{key}` post meta (for example `_sfp_price`). User fields use the same key in user meta.
- **Template of a post**: the group chosen in the editor is kept in the `_sfp_group` post meta.

## Reading values

| Function | Returns |
| --- | --- |
| `sfp_get_field( $key, $post_id = null, $format = 'raw' )` | `raw`: stored value, `plain`: plain text, `html`: formatted HTML |
| `sfp_the_field( $key, $post_id = null )` | Prints the formatted HTML |
| `sfp_get_fields( $post_id = null )` | All fields of the post: `key => raw value` |
| `sfp_fields_box( $post_id = null, $args = array() )` | Prints the ready-made field box |
| `sfp_get_field_object( $key, $post_id = null )` | Field definition + `value`, `group`, `meta_key`, `empty` |

```php
// Raw value (for calculations)
$price = (float) sfp_get_field( 'price' );

// Formatted (currency, unit, link, stars…)
sfp_the_field( 'price' );

// Plain text (titles, meta tags, aria-label)
$rooms = sfp_get_field( 'rooms', null, 'plain' );

// Field info: label, type, options
$f = sfp_get_field_object( 'property_type' );
if ( $f && ! $f['empty'] ) {
	printf( '<strong>%s:</strong> %s', esc_html( $f['label'] ), esc_html( sfp_get_field( 'property_type', null, 'plain' ) ) );
}
```

`sfp_fields_box()` arguments: `fields` (comma separated keys), `exclude`, `layout` (`cards`, `table`, `list`, `minimal`), `title`, `show_title`, `class`.

```php
sfp_fields_box( get_the_ID(), array( 'fields' => 'price,rooms,net_area', 'layout' => 'table', 'title' => 'Summary' ) );
```

## Writing and deleting values

| Function | Notes |
| --- | --- |
| `sfp_update_field( $key, $value, $post_id = null )` | Cleans the value like the editor and saves it; an empty value deletes the meta. `true` / `false` |
| `sfp_delete_field( $key, $post_id = null )` | Deletes the value and its helper values (currency, map, variations) |

```php
sfp_update_field( 'price', 2450000, $post_id );
sfp_update_field( 'features', array( 'Elevator', 'Parking' ), $post_id );   // checkbox: option values
sfp_update_field( 'team', array(                                              // repeater: rows
	array( 'name' => 'Ada', 'role' => 'Design' ),
	array( 'name' => 'Sam', 'role' => 'Code' ),
), $post_id );
sfp_delete_field( 'old_price', $post_id );
```

The `sfp_field_saved` action runs for every saved field.

## Row loops (repeater, flexible content, group, lists)

Works like ACF's `have_rows()`. Supported fields: **Repeater**, **Flexible Content**, **Group**, **Repeatable List**, **Key – Value Table**, **Grouped List** and **Grouped Key – Value**.

| Function | Notes |
| --- | --- |
| `sfp_have_rows( $key, $post_id = null )` | Is there another row? Inside a row it loops the rows of a sub field (nested) |
| `sfp_the_row()` | Moves to the next row and returns it |
| `sfp_get_sub_field( $key, $format = 'raw' )` / `sfp_the_sub_field( $key )` | Sub field of the current row |
| `sfp_get_row()` / `sfp_get_row_index()` | The whole row / 1-based index |
| `sfp_get_row_layout()` | Layout key of a flexible content row |
| `sfp_reset_rows()` | Closes loops you left early with `break` |

```php
<?php if ( sfp_have_rows( 'team' ) ) : ?>
	<ul class="team">
	<?php while ( sfp_have_rows( 'team' ) ) : sfp_the_row(); ?>
		<li>
			<b><?php echo esc_html( sfp_get_sub_field( 'name' ) ); ?></b>
			<?php sfp_the_sub_field( 'photo' ); // Formatted image ?>
		</li>
	<?php endwhile; ?>
	</ul>
<?php endif; ?>
```

Sub field keys of the list and table fields:

| Field type | Sub fields |
| --- | --- |
| Repeatable List | `value` |
| Key – Value Table | `k`, `v` |
| Grouped List | `title`, inner loop `items` (each item `value`) |
| Grouped Key – Value | `title`, inner loop `rows` (`k`, `v`) |

```php
// Grouped key – value: a "Technical specs" table
while ( sfp_have_rows( 'specs' ) ) : sfp_the_row();
	echo '<h4>' . esc_html( sfp_get_sub_field( 'title' ) ) . '</h4><dl>';
	while ( sfp_have_rows( 'rows' ) ) : sfp_the_row();
		echo '<dt>' . esc_html( sfp_get_sub_field( 'k' ) ) . '</dt><dd>' . esc_html( sfp_get_sub_field( 'v' ) ) . '</dd>';
	endwhile;
	echo '</dl>';
endwhile;
```

### Page builder with flexible content

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

## Raw values by field type

| Type | Raw value |
| --- | --- |
| Text, email, phone, URL, video | `string` |
| Number, price, rating, range | Numeric `string` (the price currency is kept in `_sfp_{key}__cur`) |
| True / False | `'1'` or `'0'` |
| Select, radio | Selected value `string` |
| Checkboxes, multi select | Array of values |
| Date / Time / Date-time | `Y-m-d` / `H:i` / `Y-m-d\TH:i` |
| Duration | Minutes `string` |
| Image, file | Attachment ID `string` → `wp_get_attachment_image( $id, 'large' )` |
| Gallery | Array of attachment IDs |
| Post object, relationship, taxonomy, user | Array of IDs (also for a single choice) |
| Link | `array( 'url', 'title', 'target' )` |
| Group / Repeater / Flexible | Array of rows (flexible rows carry the layout key in `_l`) |

```php
// Image
$img = (int) sfp_get_field( 'cover' );
echo $img ? wp_get_attachment_image( $img, 'large', false, array( 'loading' => 'lazy' ) ) : '';

// Relationship: related posts
foreach ( (array) sfp_get_field( 'related' ) as $rid ) {
	printf( '<a href="%s">%s</a>', esc_url( get_permalink( $rid ) ), esc_html( get_the_title( $rid ) ) );
}
```

## Theme examples

### Single page: a property listing (single.php)

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

### Archive card (archive.php / loop)

```php
<article class="card">
	<a href="<?php the_permalink(); ?>"><?php the_post_thumbnail( 'medium' ); ?></a>
	<h2><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>
	<p><?php echo esc_html( sfp_get_field( 'rooms', null, 'plain' ) ); ?> · <?php echo esc_html( sfp_get_field( 'net_area', null, 'plain' ) ); ?></p>
	<strong><?php sfp_the_field( 'price' ); ?></strong>
</article>
```

### Query by field value (WP_Query)

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

Array values such as checkboxes are stored serialized; to find one value use `'compare' => 'LIKE'` with `'value' => '"Parking"'`.

### User fields (profile)

```php
$city = sfp_api()->user_get( get_current_user_id(), 'location' );
echo sfp_api()->display( 'GROUP_ID', 'user', $user_id ); // Profile cards
```

### Front-end form (for members)

```php
sfp_api()->enqueue_form();
echo '<form method="post">' . wp_nonce_field( 'my_save', '_n', true, false )
	. sfp_api()->form( array( 'g123' ), array( 'prefix' => 'my_fields', 'id' => $post_id ) )
	. '<button>Save</button></form>';

// On submit:
if ( isset( $_POST['_n'] ) && wp_verify_nonce( $_POST['_n'], 'my_save' ) && current_user_can( 'edit_post', $post_id ) ) {
	$gid    = sanitize_key( $_POST['sfp_group'] );
	$result = sfp_api()->save( 'post', $post_id, $gid, wp_unslash( $_POST['my_fields'][ $gid ] ) );
	if ( is_wp_error( $result ) ) {
		echo esc_html( $result->get_error_message() );
	}
}
```

## Moving from ACF

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
| Local JSON (`acf-json`) | `sfp-json` folder in the theme |

For a quick switch you can add a temporary bridge to your theme:

```php
if ( ! function_exists( 'get_field' ) && function_exists( 'sfp_get_field' ) ) {
	function get_field( $key, $post_id = null ) { return sfp_get_field( $key, $post_id ); }
	function the_field( $key, $post_id = null ) { sfp_the_field( $key, $post_id ); }
}
```

## Groups in code and JSON sync

### A group in code

Give it a fixed `id`; when the array changes the group is updated once in wp-admin.

```php
add_action( 'init', function () {
	if ( ! function_exists( 'sfp_add_local_group' ) ) {
		return;
	}
	sfp_add_local_group( array(
		'id'         => 'gthemehero',
		'title'      => 'Page Hero',
		'post_types' => array( 'page' ),
		'active'     => 1,
		'fields'     => array(
			array( 'key' => 'hero_title', 'label' => 'Title', 'type' => 'text', 'width' => 'full' ),
			array( 'key' => 'hero_image', 'label' => 'Image', 'type' => 'image' ),
			array( 'key' => 'hero_link', 'label' => 'Button', 'type' => 'link' ),
		),
	) );
} );
```

### JSON files (version control with git)

1. Create a `sfp-json` folder in your theme (`wp-content/themes/your-theme/sfp-json`).
2. Saving a group writes it there as `{id}.json`.
3. When the files are newer on another site they are imported automatically on the next wp-admin visit.

```php
// Another folder:
add_filter( 'sfp_json_save_path', fn() => WP_CONTENT_DIR . '/sfp-json' );
add_filter( 'sfp_json_load_paths', fn( $paths ) => array_merge( $paths, array( get_template_directory() . '/sfp-json' ) ) );
```

## sfp_api() methods

| Method | Returns |
| --- | --- |
| `version()` | Plugin version |
| `modules()` | Active modules: `shop`, `woocommerce`, `appointments`, `reviews`, `social`, `forms`, `video`, `ads`, `currency`, `search` |
| `pages()` | Cart, checkout, order, appointment and account page URLs |
| `groups( array( 'post_type' => 'post', 'kind' => 'shop' ) )` | Active groups |
| `group( $id )` / `fields( $group_or_id )` | One group / its field list (translated for visitors) |
| `get( $key, $post_id, $format )` / `get_all( $post_id, 'plain' )` | One value / all visible values |
| `user_get( $user_id, $key )` | Raw value of a user field |
| `save( 'post' \| 'user', $id, $group_id, $values, $args )` | `true` or `WP_Error` (nothing is written when a required field is empty) |
| `enqueue_form()` / `form( $groups, $args )` | Front-end field editor |
| `display( $group_id, 'user', $user_id )` | Profile cards for users |
| `account_tabs( $user_id )` | Orders, address and appointment tabs |
| `member_template()` | ID of the "Member Profile" group |

```php
$modules = sfp_api()->modules();
if ( ! empty( $modules['shop'] ) ) {
	echo '<a href="' . esc_url( sfp_api()->pages()['cart'] ?? '' ) . '">Cart</a>';
}
```

## REST API

Base: `/wp-json/sfp/v1`

| Route | Notes |
| --- | --- |
| `GET /groups?post_type=post` | Public |
| `GET /posts/{id}?format=plain` | Published posts (or posts you can read) |
| `POST /posts/{id}` | `{"group":"g123","fields":{"price":"99"}}` — needs `edit_post` and the `X-WP-Nonce` header |
| `GET /users/{id}/profile` | Public profile fields (Smart Social Pro) |

```js
fetch( '/wp-json/sfp/v1/posts/42?format=plain' )
	.then( ( r ) => r.json() )
	.then( ( d ) => console.log( d.fields.price ) );
```

## JavaScript

```js
SFPFront.rest( 'posts/42' ).then( ( d ) => console.log( d.fields ) );
SFPFront.rest( 'posts/42', { body: { fields: { price: '120' } } } ); // logged-in users
document.addEventListener( 'sfp:cart', ( e ) => console.log( e.detail.count ) );
SFPFront.lightbox( [ { type: 'image', src: '/big.jpg', thumb: '/small.jpg' } ], 0, 'Title' );
```

## Hooks

**Actions:** `sfp_field_saved`, `sfp_saved_post`, `sfp_group_saved`, `sfp_api_saved`, `sfp_api_ready`, `sfp_order_created`, `sfp_order_status`, `sfp_order_paid`, `sfp_stock_changed`, `sfp_appt_created`, `sfp_appt_status`, `sfp_form_submitted`.

**Filters:** `sfp_field_types`, `sfp_templates`, `sfp_schema_data`, `sfp_format_value`, `sfp_plain_value`, `sfp_render_box`, `sfp_account_tabs`, `sfp_account_url`, `sfp_profile_group`, `sfp_carriers`, `sfp_search_query_args`, `sfp_menu_location`, `sfp_json_save_path`, `sfp_json_load_paths`.

```php
// Add a property to the JSON-LD
add_filter( 'sfp_schema_data', function ( $data, $post_id ) {
	$data['award'] = sfp_get_field( 'award', $post_id, 'plain' );
	return $data;
}, 10, 2 );

// Change how a field looks
add_filter( 'sfp_format_value', function ( $html, $field, $value, $post_id ) {
	return 'price' === $field['key'] ? '<mark>' . $html . '</mark>' : $html;
}, 10, 4 );

// When an order is paid
add_action( 'sfp_order_paid', function ( $order_id ) {
	error_log( 'Paid: ' . $order_id );
} );
```

These actions also work as triggers in automation tools that listen to WordPress hooks (Uncanny Automator, WP Webhooks…).

## Shortcodes and blocks

`[sfp_fields]`, `[sfp_field key=""]`, `[sfp_gallery]`, `[sfp_search]`, `[sfp_form id=""]`, `[sfp_reviews]`, `[sfp_rating]`, `[sfp_reviews_page]`, `[sfp_currency]`, `[sfp_cart]`, `[sfp_checkout]`, `[sfp_order]`, `[sfp_account]`, `[sfp_booking]`, `[sfp_appointment]`, `[sfp_seller]`.

Blocks: **Smart Fields** and **Smart Fields Search**. Elementor dynamic tags, Bricks `{sfp_key}`, Yoast `%%sfp_key%%`, Rank Math `%sfp_key%`, Gravity Forms / WPForms `{sfp:key}`, Fluent Forms `{sfp_key}`.

## Modules

A module switched off under Settings → Modules loads no code on the site. To check it in a theme:

```php
if ( class_exists( 'SFP_Modules' ) && SFP_Modules::on( 'reviews' ) ) {
	echo do_shortcode( '[sfp_rating]' );
}
```

Shortcodes of a switched-off module print nothing, so no raw `[shortcode]` shows on the page.

## CSS variables

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
