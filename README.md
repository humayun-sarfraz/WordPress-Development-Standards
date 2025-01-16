# WordPress Theme Development Guide

## **Overview**
A WordPress theme determines the look and feel of a website, controlling the layout, design, and visual appearance. Themes enable developers to define templates for posts, pages, and custom content, providing users with customization options.

### **Key Features of a WordPress Theme**
1. **Customization**: Modify visual aspects via the Customizer API.
2. **Templates**: Control layouts for pages, posts, and archives.
3. **Widgets and Menus**: Add interactive elements.
4. **Internationalization**: Ensure compatibility with multiple languages.
5. **Accessibility**: Provide inclusive designs.

### **Importance of Adhering to Standards**
- **Maintainability**: Code that follows WordPress standards is easier to debug and update.
- **Security**: Reduces vulnerabilities through proper sanitization and escaping.
- **Compatibility**: Ensures seamless integration with plugins and core updates.

---

## **Folder Structure**
Organizing your theme files systematically improves readability and scalability. Below is a typical folder structure:

```
/theme-name
├── style.css          // Theme metadata and global styles
├── functions.php      // Theme setup and functions
├── index.php          // Fallback template
├── header.php         // Header template
├── footer.php         // Footer template
├── sidebar.php        // Sidebar template
├── assets/            // CSS, JS, and images
│   ├── css/
│   │   └── main.css
│   ├── js/
│   │   └── main.js
│   └── images/
│       └── logo.png
├── inc/               // Included PHP files
│   ├── customizer.php
│   ├── setup.php
│   └── template-tags.php
├── languages/         // Translation files
│   └── theme-name.pot
├── template-parts/    // Reusable template parts
│   ├── header/
│   │   └── site-header.php
│   └── footer/
│       └── site-footer.php
├── templates/         // Custom templates
│   └── custom-template.php
├── 404.php            // 404 error template
├── archive.php        // Archive template
└── page.php           // Page template
```

### **File Purposes**
- **style.css**: Defines theme metadata (e.g., `Theme Name`, `Author`) and global styles.
- **functions.php**: Initializes theme features (e.g., menus, widgets).
- **index.php**: The fallback template used if no other template matches.
- **header.php** and **footer.php**: Contain the opening and closing HTML tags.
- **assets/**: Contains all static resources.
- **template-parts/**: Modular template sections, reusable across pages.
- **languages/**: Translation-ready files.

---

## **Template Hierarchy**
WordPress’s template hierarchy determines which template files are used based on the requested content type.

### **Examples**
1. **Single Posts**:
   - `single.php`: Default single post template.
   - `single-{post-type}.php`: Template for specific custom post types (e.g., `single-product.php`).

2. **Pages**:
   - `page.php`: General page template.
   - `page-{slug}.php`: Template for specific pages by slug (e.g., `page-about.php`).

3. **Archives**:
   - `archive.php`: General archive template.
   - `category.php`, `tag.php`: Templates for specific taxonomies.

4. **Other Templates**:
   - `404.php`: Template for not-found errors.
   - `search.php`: Template for search results.

### **Using Template Tags**
Template tags allow dynamic content retrieval:
```php
<?php the_title(); ?>
<?php the_content(); ?>
<?php get_template_part( 'template-parts/header/site-header' ); ?>
```

### **Conditional Tags**
Control template behavior based on conditions:
```php
if ( is_single() ) {
    echo 'This is a single post.';
}
if ( is_category( 'news' ) ) {
    echo 'Category: News';
}
```

---

## **Enqueueing Styles and Scripts**
Properly enqueue assets to prevent conflicts and ensure dependencies are loaded.

```php
function theme_enqueue_scripts() {
    wp_enqueue_style( 'theme-style', get_stylesheet_uri(), [], '1.0.0', 'all' );
    wp_enqueue_script( 'theme-script', get_template_directory_uri() . '/assets/js/main.js', [ 'jquery' ], '1.0.0', true );
}
add_action( 'wp_enqueue_scripts', 'theme_enqueue_scripts' );
```

### **Versioning**
Use version numbers to prevent browser caching issues.

---

## **Theme Options**
Implement theme customization via the WordPress Customizer API.

```php
function theme_customize_register( $wp_customize ) {
    $wp_customize->add_section( 'theme_options', [
        'title'    => __( 'Theme Options', 'theme-name' ),
        'priority' => 30,
    ]);

    $wp_customize->add_setting( 'primary_color', [
        'default'           => '#ffffff',
        'sanitize_callback' => 'sanitize_hex_color',
    ]);

    $wp_customize->add_control( new WP_Customize_Color_Control( $wp_customize, 'primary_color', [
        'label'    => __( 'Primary Color', 'theme-name' ),
        'section'  => 'theme_options',
    ]));
}
add_action( 'customize_register', 'theme_customize_register' );
```

---

## **Internationalization (i18n) and Localization (l10n)**
Make your theme translatable by following these steps:

### **1. Load Text Domain**
```php
function theme_setup() {
    load_theme_textdomain( 'theme-name', get_template_directory() . '/languages' );
}
add_action( 'after_setup_theme', 'theme_setup' );
```

### **2. Use Translation Functions**
```php
echo esc_html__( 'Welcome to my site!', 'theme-name' );
_e( 'Contact Us', 'theme-name' );
printf( esc_html__( 'Hello, %s!', 'theme-name' ), esc_html( $user_name ) );
```

### **3. Generate .pot Files**
Use tools like Poedit or WP-CLI:
```bash
wp i18n make-pot . languages/theme-name.pot
```

---

## **Accessibility**
Ensure your theme is inclusive by adhering to accessibility standards.

### **Key Guidelines**
1. **Semantic HTML5 Elements**:
   ```html
   <header>
       <nav aria-label="Primary Navigation">
           <?php wp_nav_menu( [ 'theme_location' => 'primary' ] ); ?>
       </nav>
   </header>
   ```

2. **Skip Links**:
   ```html
   <a href="#main-content" class="skip-link screen-reader-text">Skip to content</a>
   ```

3. **ARIA Attributes**:
   ```html
   <button aria-expanded="false" aria-controls="menu">Menu</button>
   ```

4. **Alt Attributes**:
   ```html
   <img src="<?php echo esc_url( $image_url ); ?>" alt="<?php echo esc_attr( $alt_text ); ?>">
   ```

---

## **Best Practices**
1. **Use `!function_exists`**:
   Prevent function redeclaration.
   ```php
   if ( ! function_exists( 'theme_setup' ) ) {
       function theme_setup() {
           add_theme_support( 'title-tag' );
           add_theme_support( 'post-thumbnails' );
       }
   }
   add_action( 'after_setup_theme', 'theme_setup' );
   ```

2. **Sanitize Inputs**:
   ```php
   $sanitized_input = sanitize_text_field( $_POST['input_field'] );
   ```

3. **Escape Outputs**:
   ```php
   echo esc_html( $sanitized_input );
   ```

4. **Optimize Performance**:
   - Lazy load images.
   - Minify CSS/JS files.

---

## **Theme Testing**
1. Validate with the [Theme Check plugin](https://wordpress.org/plugins/theme-check/).
2. Test responsiveness across devices.
3. Ensure compatibility with major plugins.

---

## **Packaging and Documentation**
1. **Prepare for Distribution**:
   - Include only necessary files.
   - Minify assets.

2. **README.md Example**:
   ```markdown
   # Theme Name

   ## Description
   A brief description of your theme.

   ## Installation
   1. Upload the theme to `/wp-content/themes/`.
   2. Activate it via the WordPress dashboard.
   ```

---

## **Advanced Topics**
1. **Child Themes**:
   - Use child themes for overriding parent themes without modifying the original code.

2. **Theme Hooks**:
   - Utilize `do_action` and `apply_filters` to customize functionality.

3. **Block-Based Themes**:
   - Explore Full Site Editing (FSE) and block templates.

---

By following this guide, developers can create secure, scalable, and user-friendly WordPress themes that adhere to modern best practices.







# WordPress Plugin Development Guide

## **Overview**
WordPress plugins are essential tools to extend and customize WordPress functionalities. Plugins allow developers to add features, improve performance, and modify WordPress behavior without altering the core code.

### **Key Features of a WordPress Plugin**
1. **Extendibility**: Add or modify features using hooks (actions and filters).
2. **Modularity**: Keep functionality separate from themes.
3. **Reusability**: Share plugins across multiple websites.
4. **Scalability**: Build features that grow with the website's needs.

### **Importance of Adhering to Standards**
- **Maintainability**: Ensures plugins are easy to understand and update.
- **Security**: Prevent vulnerabilities by following best practices.
- **Compatibility**: Avoid conflicts with other plugins, themes, or core updates.

---

## **Folder Structure**
Organizing your plugin files systematically improves readability and scalability. Below is a typical folder structure:

```
/plugin-name
├── plugin-name.php    // Main plugin file
├── includes/          // Core plugin functionality
│   ├── class-my-plugin.php
│   ├── functions.php
│   └── admin.php
├── assets/            // CSS, JS, and images
│   ├── css/
│   │   └── admin.css
│   ├── js/
│   │   └── admin.js
│   └── images/
│       └── icon.png
├── languages/         // Translation files
│   └── plugin-name.pot
├── templates/         // Custom feature templates
│   └── admin-template.php
├── uninstall.php      // Cleanup on uninstall
├── README.md          // Plugin documentation
└── LICENSE.txt        // License information
```

### **File Purposes**
- **plugin-name.php**: The main file that initializes the plugin and contains metadata.
- **includes/**: Houses PHP files for core functionality.
- **assets/**: Stores static assets like CSS, JavaScript, and images.
- **languages/**: Contains translation-ready `.pot` files.
- **templates/**: Holds reusable templates for plugin features.
- **uninstall.php**: Cleans up plugin data during uninstallation.

---

## **Plugin Header**
Every plugin must include a file with a header comment block. This block provides metadata to WordPress:

```php
<?php
/*
Plugin Name: My Custom Plugin
Plugin URI: https://example.com/my-plugin
Description: A custom plugin for managing features.
Version: 1.0.0
Author: Your Name
Author URI: https://example.com
License: GPLv2 or later
Text Domain: my-plugin
*/
```
---

## **Activation, Deactivation, and Uninstallation**
### **Activation and Deactivation Hooks**
Use hooks to perform setup tasks when the plugin is activated or deactivated:

```php
if ( ! function_exists( 'plugin_activate' ) ) {
    function plugin_activate() {
        // Setup tasks (e.g., create custom database tables)
        flush_rewrite_rules();
    }
}
register_activation_hook( __FILE__, 'plugin_activate' );

if ( ! function_exists( 'plugin_deactivate' ) ) {
    function plugin_deactivate() {
        // Cleanup tasks (e.g., remove scheduled events)
        flush_rewrite_rules();
    }
}
register_deactivation_hook( __FILE__, 'plugin_deactivate' );
```

### **Uninstall Hook**
The `uninstall.php` file should clean up all data added by the plugin:

```php
if ( ! defined( 'WP_UNINSTALL_PLUGIN' ) ) {
    exit;
}

// Delete plugin options
delete_option( 'my_plugin_option' );

// Remove custom database tables
global $wpdb;
$wpdb->query( "DROP TABLE IF EXISTS {$wpdb->prefix}my_plugin_table" );
```

---

## **Using Hooks (Actions and Filters)**
Hooks allow plugins to interact with WordPress core or other plugins.

### **Action Hooks**
Actions execute custom code at specific points:
```php
function my_plugin_add_scripts() {
    wp_enqueue_style( 'my-plugin-style', plugin_dir_url( __FILE__ ) . 'assets/css/admin.css', [], '1.0.0', 'all' );
}
add_action( 'admin_enqueue_scripts', 'my_plugin_add_scripts' );
```

### **Filter Hooks**
Filters modify data before it is displayed or processed:
```php
function my_plugin_modify_title( $title ) {
    return $title . ' - Customized';
}
add_filter( 'the_title', 'my_plugin_modify_title' );
```

---

## **Database Interactions**
### **Secure Queries**
Always use `$wpdb->prepare()` to prevent SQL injection:
```php
global $wpdb;
$query = $wpdb->prepare( "SELECT * FROM {$wpdb->prefix}my_table WHERE id = %d", $id );
$results = $wpdb->get_results( $query );
```

---

## **AJAX Implementation**
Secure AJAX requests using nonces.

### **Enqueue Scripts**
```php
function my_plugin_enqueue_scripts() {
    wp_enqueue_script( 'my-plugin-ajax', plugin_dir_url( __FILE__ ) . 'assets/js/ajax.js', [ 'jquery' ], '1.0.0', true );
    wp_localize_script( 'my-plugin-ajax', 'myPluginAjax', [
        'ajax_url' => admin_url( 'admin-ajax.php' ),
        'nonce'    => wp_create_nonce( 'my_plugin_nonce' ),
    ]);
}
add_action( 'wp_enqueue_scripts', 'my_plugin_enqueue_scripts' );
```

### **Handle AJAX Requests**
```php
function my_plugin_handle_ajax() {
    check_ajax_referer( 'my_plugin_nonce', 'nonce' );

    $data = sanitize_text_field( $_POST['data'] );

    wp_send_json_success( [ 'message' => 'Data received: ' . esc_html( $data ) ] );
}
add_action( 'wp_ajax_my_plugin_action', 'my_plugin_handle_ajax' );
add_action( 'wp_ajax_nopriv_my_plugin_action', 'my_plugin_handle_ajax' );
```

---

## **Internationalization (i18n) and Localization (l10n)**
Make your plugin translatable:

### **Load Text Domain**
```php
function my_plugin_load_textdomain() {
    load_plugin_textdomain( 'my-plugin', false, dirname( plugin_basename( __FILE__ ) ) . '/languages' );
}
add_action( 'plugins_loaded', 'my_plugin_load_textdomain' );
```

### **Use Translation Functions**
```php
echo esc_html__( 'Hello, World!', 'my-plugin' );
printf( esc_html__( 'Hello, %s!', 'my-plugin' ), esc_html( $user_name ) );
```

---

## **Best Practices**
1. **Sanitize Inputs**:
   ```php
   $input = sanitize_text_field( $_POST['input_field'] );
   ```
2. **Escape Outputs**:
   ```php
   echo esc_html( $input );
   ```
3. **Use Nonces for Security**:
   ```php
   wp_nonce_field( 'save_action', 'save_nonce' );
   ```

---

## **Testing and Debugging**
1. Enable debugging in `wp-config.php`:
   ```php
   define( 'WP_DEBUG', true );
   define( 'WP_DEBUG_LOG', true );
   ```
2. Use the Query Monitor plugin for debugging database queries and hooks.

---

## **Packaging and Documentation**
1. **Prepare Files**:
   - Exclude unnecessary files using `.gitignore`.
2. **README.md Example**:
   ```markdown
   # Plugin Name

   ## Description
   A brief description of your plugin.

   ## Installation
   1. Upload the plugin folder to `/wp-content/plugins/`.
   2. Activate the plugin through the WordPress admin.
   ```

---

By following this guide, developers can create secure, scalable, and efficient WordPress plugins that adhere to modern best practices.

