---
layout: post
title: Create a Custom Elementor Widget that Displays a Download Button for Digital Products
author: Zafran Tsany
tags: [tutorial]
category: [wordpress, woocommerce]
---

To create a custom Elementor widget that displays a download button for digital products based on the user's login status, follow these steps:

1. Create a new custom widget class in your `tag-icons-widget.php` file:

```php
class Download_Button_Widget extends \Elementor\Widget_Base {
    public function get_name() {
        return 'download_button';
    }

    public function get_title() {
        return __('Download Button', 'my-elementor-widget');
    }

    public function get_icon() {
        return 'eicon-download';
    }

    public function get_categories() {
        return array('general');
    }

    protected function render() {
        global $product;

        // Check if the current product is a downloadable product
        if ($product && $product->is_downloadable()) {
            // Check if the user is logged in
            if (is_user_logged_in()) {
                // Get the download links for the product
                $downloads = $product->get_downloads();
                if (!empty($downloads)) {
                    foreach ($downloads as $download) {
                        echo '<a class="elementor-button elementor-size-sm" href="' . esc_url($download['file']) . '" download>' . __('Download', 'my-elementor-widget') . '</a>';
                    }
                }
            } else {
                // Redirect to the login page
                $login_url = wp_login_url(get_permalink());
                echo '<a class="elementor-button elementor-size-sm" href="' . esc_url($login_url) . '">' . __('Login to Download', 'my-elementor-widget') . '</a>';
            }
        }
    }
}
```

2. Register the custom widget:

Add the following code to your `tag-icons-widget.php` file, outside of the `Download_Button_Widget` class:

```php
\Elementor\Plugin::instance()->widgets_manager->register_widget_type(new Download_Button_Widget());
```

Now, you should see a new custom widget called "Download Button" in your Elementor editor.

1. Open your Elementor template for single products.
2. Search for the "Download Button" widget in the Elementor widgets panel.
3. Drag and drop the "Download Button" widget to the desired location in your template.

Save your template, and the download button should appear for digital products based on the user's login status. If the user is not logged in, the button will redirect them to the login page. If the user is logged in, the button will allow them to download the digital product.