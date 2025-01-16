
# WordPress Development Standards Guide

## Overview
This repository provides a detailed guide on WordPress development standards for creating secure, high-quality, and performant themes and plugins. It adheres to official WordPress coding practices and includes guidelines for security, performance, and compatibility. The guide is optimized for PHP 8.3 and leverages modern PHP features to enhance code quality and maintainability.

---

## Table of Contents
1. [General Standards](#general-standards)
2. [Theme Development Standards](#theme-development-standards)
3. [Plugin Development Standards](#plugin-development-standards)
4. [Coding Style](#coding-style)
5. [Testing and Debugging](#testing-and-debugging)
6. [Documentation and Deployment](#documentation-and-deployment)
7. [License](#license)
8. [Contributing](#contributing)
9. [Additional Resources](#additional-resources)

---

## General Standards

### Coding Standards

- Follow WordPress PHP, CSS, HTML, and JavaScript coding standards.
- Use **4 spaces for indentation**, avoid tabs.
- Use descriptive variable and function names in snake_case.

#### Examples:
```php
// Correct usage
if ( ! function_exists( 'custom_function' ) ) {
    function custom_function( string $name ): string {
        return 'Hello, ' . esc_html( $name );
    }
}

// Incorrect usage
function customFunction($name){
return 'Hello '.$name;
}
```

### Security Best Practices

#### Sanitize Inputs:
```php
$user_input = isset( $_POST['username'] ) ? sanitize_text_field( $_POST['username'] ) : '';
```

#### Escape Outputs:
```php
echo esc_html( $user_input );
```

#### Use Nonces for Security:
```php
wp_nonce_field( 'save_action', 'save_nonce' );
if ( ! isset( $_POST['save_nonce'] ) || ! wp_verify_nonce( $_POST['save_nonce'], 'save_action' ) ) {
    wp_die( esc_html__( 'Security check failed.', 'text-domain' ) );
}
```

### Performance Optimization

#### Database Queries:
```php
global $wpdb;
$query = $wpdb->prepare( "SELECT ID, user_login FROM {$wpdb->users} WHERE ID = %d", 1 );
$results = $wpdb->get_results( $query );
```

#### Caching:
```php
$cached_data = get_transient( 'my_plugin_cached_data' );
if ( false === $cached_data ) {
    $cached_data = 'Expensive operation result';
    set_transient( 'my_plugin_cached_data', $cached_data, HOUR_IN_SECONDS );
}
echo $cached_data;
```

---

## Theme Development Standards

### Folder Structure
```
/theme-name
├── style.css
├── functions.php
├── index.php
├── header.php
├── footer.php
├── assets/
│   ├── css/
│   │   └── main.css
│   ├── js/
│   │   └── main.js
├── inc/
│   ├── customizer.php
│   ├── setup.php
│   └── template-tags.php
├── languages/
│   └── theme-name.pot
├── templates/
│   └── custom-template.php
```

### Enqueue Styles and Scripts
```php
function theme_enqueue_scripts(): void {
    wp_enqueue_style( 'main-style', get_stylesheet_uri(), [], '1.0.0', 'all' );
    wp_enqueue_script( 'main-script', get_template_directory_uri() . '/assets/js/main.js', [ 'jquery' ], '1.0.0', true );
}
add_action( 'wp_enqueue_scripts', 'theme_enqueue_scripts' );
```

---

## Plugin Development Standards

### Folder Structure
```
/plugin-name
├── plugin-name.php
├── includes/
│   ├── class-my-plugin.php
│   ├── functions.php
│   └── admin.php
├── assets/
│   ├── css/
│   │   └── admin.css
│   ├── js/
│   │   └── admin.js
├── languages/
│   └── my-plugin.pot
```

### Activation and Deactivation
```php
if ( ! function_exists( 'plugin_activate' ) ) {
    function plugin_activate(): void {
        flush_rewrite_rules();
    }
}
register_activation_hook( __FILE__, 'plugin_activate' );
```

---

## Coding Style
### Standards
- Use `! function_exists` to avoid redeclaring functions.
- Escape all outputs before rendering.

### Examples:
```php
<h1><?php echo esc_html( get_the_title() ); ?></h1>
```

---

## Testing and Debugging

### Debugging Tools
Enable debugging in `wp-config.php`:
```php
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );
define( 'WP_DEBUG_DISPLAY', false );
```

---

## Documentation and Deployment

### README Files
Include comprehensive documentation with:
1. Description
2. Features
3. Installation Instructions
4. Usage Instructions

---

## License
This project is licensed under the GPLv2 or later.

---

## Contributing
Contributions are welcome! Fork the repository and submit a pull request.

---

## Additional Resources
- [WordPress Developer Handbook](https://developer.wordpress.org/)
- [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/)
- [Theme Development](https://developer.wordpress.org/themes/)
- [Plugin Development](https://developer.wordpress.org/plugins/)
