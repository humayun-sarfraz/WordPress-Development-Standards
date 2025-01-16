
# WordPress Development Standards Guide

## Overview
This repository provides a detailed guide on WordPress development standards for creating secure, high-quality, and performant themes and plugins. It adheres to official WordPress coding practices and includes guidelines for security, performance, and compatibility.

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

---

## General Standards
### Coding Standards
- Follow WordPress PHP, CSS, HTML, and JavaScript coding standards.
- Use **4 spaces for indentation**, avoid tabs.
- Use descriptive variable and function names in snake_case.

### Security Best Practices
- **Sanitize Inputs**: 
  ```php
  $user_input = isset( $_POST['username'] ) ? sanitize_text_field( $_POST['username'] ) : '';
  ```

- **Escape Outputs**:
  ```php
  echo esc_html( $user_input );
  ```

- **Use Nonces for Security**:
  ```php
  wp_nonce_field( 'save_action', 'save_nonce' );
  if ( ! wp_verify_nonce( $_POST['save_nonce'], 'save_action' ) ) {
      wp_die( esc_html__( 'Security check failed.', 'text-domain' ) );
  }
  ```

- **Validate and Sanitize URLs**:
  ```php
  $url = isset( $_POST['website'] ) ? esc_url_raw( $_POST['website'] ) : '';
  ```

### Performance Optimization
- Optimize database queries; avoid using `SELECT *` and use `$wpdb->prepare()`.
- Combine and minify CSS/JS files to reduce HTTP requests.
- Use caching mechanisms (transients or object cache).

---

## Theme Development Standards
### Folder Structure
Organize your theme as follows:
```
/theme-name
    ├── style.css          // Theme metadata and global styles
    ├── functions.php      // Theme setup functions
    ├── index.php          // Main template
    ├── header.php         // Header template
    ├── footer.php         // Footer template
    ├── assets/            // CSS, JS, and images
    └── templates/         // Reusable template parts
```

### Template Hierarchy
Use WordPress template hierarchy to structure theme files. Example:
- Single posts: `single.php`, `single-{post-type}.php`.
- Pages: `page.php`, `page-{slug}.php`.
- Archives: `archive.php`, `archive-{post-type}.php`.

### Enqueue Styles and Scripts
```php
function theme_enqueue_scripts() {
    wp_enqueue_style( 'main-style', get_stylesheet_uri() );
    wp_enqueue_script( 'main-script', get_template_directory_uri() . '/assets/js/script.js', array( 'jquery' ), '1.0', true );
}
add_action( 'wp_enqueue_scripts', 'theme_enqueue_scripts' );
```

### Accessibility
- Use semantic HTML and ARIA roles.
- Ensure proper color contrast for readability.
- Provide keyboard navigation and screen reader compatibility.

---

## Plugin Development Standards
### Folder Structure
Organize your plugin files as follows:
```
/plugin-name
    ├── plugin-name.php    // Main plugin file
    ├── includes/          // Core plugin functionality
    ├── assets/            // CSS, JS, and images
    ├── languages/         // Translation files
    └── templates/         // Custom feature templates
```

### Activation and Deactivation
```php
if ( ! function_exists( 'plugin_activate' ) ) {
    function plugin_activate() {
        // Activation logic
        flush_rewrite_rules();
    }
}
register_activation_hook( __FILE__, 'plugin_activate' );

if ( ! function_exists( 'plugin_deactivate' ) ) {
    function plugin_deactivate() {
        // Deactivation logic
        flush_rewrite_rules();
    }
}
register_deactivation_hook( __FILE__, 'plugin_deactivate' );
```

### Secure AJAX Implementation
```php
function handle_ajax_request() {
    check_ajax_referer( 'my_nonce', 'security' );
    $response = array( 'message' => esc_html__( 'Success!', 'text-domain' ) );
    wp_send_json_success( $response );
}
add_action( 'wp_ajax_my_action', 'handle_ajax_request' );
add_action( 'wp_ajax_nopriv_my_action', 'handle_ajax_request' );
```

### Database Interactions
Use `$wpdb->prepare()` for secure database queries:
```php
global $wpdb;
$user_id = 1;
$query = $wpdb->prepare( "SELECT * FROM {$wpdb->users} WHERE ID = %d", $user_id );
$results = $wpdb->get_results( $query );
```

---

## Coding Style
### Function Existence Check
Use `! function_exists` to avoid redeclaring functions:
```php
if ( ! function_exists( 'custom_function' ) ) {
    function custom_function() {
        // Code here
    }
}
```

### Escape All Outputs
Always escape outputs before rendering:
```php
<h1><?php echo esc_html( get_the_title() ); ?></h1>
```

### Translation and Localization
Use translation functions for all user-facing text:
```php
echo esc_html__( 'Hello, World!', 'text-domain' );
```

Use placeholders in translations:
```php
printf( esc_html__( 'You have %d new messages.', 'text-domain' ), $message_count );
```

---

## Testing and Debugging
### PHPUnit Tests
```php
class PluginTest extends WP_UnitTestCase {
    public function test_function_exists() {
        $this->assertTrue( function_exists( 'plugin_function' ) );
    }
}
```

### Debugging Tools
Enable debugging in `wp-config.php`:
```php
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );
```

Use Query Monitor to debug hooks, queries, and performance issues.

---

## Documentation and Deployment
### README Files
Include the following in your `README.md`:
1. Plugin/Theme Description
2. Installation Instructions
3. Changelog
4. License Details

### Versioning
Use semantic versioning (`MAJOR.MINOR.PATCH`) for updates.

### Packaging
Exclude unnecessary files and include only production-ready files (e.g., `.git`, `.svn`, `.DS_Store`).

---

## License
This project is licensed under the GPLv2 or later.

---

## Contributing
Contributions are welcome! Fork the repository and submit a pull request with improvements.
