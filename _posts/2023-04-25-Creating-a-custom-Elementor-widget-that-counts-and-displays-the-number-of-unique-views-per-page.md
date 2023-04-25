---
layout: post
title: Creating a custom Elementor widget that counts and displays the number of unique views per page
author: Zafran Tsany
tags: [tutorial]
category: [Wordpress, Woocommerce]
---

Creating a custom Elementor widget that counts and displays the number of unique views per page requires two main components:

1. A PHP function to track and store the unique views.
2. A custom Elementor widget to display the view count.

First, let's create the PHP function to track and store the unique views. Add the following code to your custom plugin's main file (`my-elementor-widget.php`):

```php
// Unique view counter function
function my_elementor_widget_track_unique_views() {
    // Get the current post ID
    $post_id = get_the_ID();
    // Get the user's IP address
    $user_ip = $_SERVER['REMOTE_ADDR'];
    // Get the stored views data
    $views_data = get_post_meta($post_id, '_unique_views_data', true);

    // If there's no data, initialize an empty array
    if (empty($views_data)) {
        $views_data = array();
    } else {
        // Otherwise, unserialize the stored data
        $views_data = maybe_unserialize($views_data);
    }

    // If the user's IP is not in the stored data, increment the view count
    if (!in_array($user_ip, $views_data)) {
        // Add the user's IP to the stored data
        $views_data[] = $user_ip;
        // Update the post meta with the new data
        update_post_meta($post_id, '_unique_views_data', maybe_serialize($views_data));
    }
}
// Hook the function to 'wp_head' to track views on each page load
add_action('wp_head', 'my_elementor_widget_track_unique_views');
```

Next, create a new custom Elementor widget to display the view count. In your `tag-icons-widget.php` file, add the following code for the new widget class:

```php
class Unique_Views_Widget extends \Elementor\Widget_Base {
    public function get_name() {
        return 'unique_views';
    }

    public function get_title() {
        return __('Unique Views', 'my-elementor-widget');
    }

    public function get_icon() {
        return 'eicon-eye';
    }

    public function get_categories() {
        return array('general');
    }

    protected function render() {
        // Get the current post ID
        $post_id = get_the_ID();
        // Get the stored views data
        $views_data = get_post_meta($post_id, '_unique_views_data', true);

        // If there's data, unserialize and count the views
        if (!empty($views_data)) {
            $views_data = maybe_unserialize($views_data);
            $view_count = count($views_data);
        } else {
            $view_count = 0;
        }

        // Display the view count
        echo '<div class="unique-views-count">' . $view_count . ' ' . __('Views', 'my-elementor-widget') . '</div>';
    }
}

// Register the custom widget
\Elementor\Plugin::instance()->widgets_manager->register_widget_type(new Unique_Views_Widget());
```

Now, you should see a new custom widget called "Unique Views" in your Elementor editor.

1. Open your Elementor template.
2. Search for the "Unique Views" widget in the Elementor widgets panel.
3. Drag and drop the "Unique Views" widget to the desired location in your template.

Save your template, and the widget should display the unique view count for each page on your website. Please note that this is a simple implementation and may not be suitable for high-traffic websites or websites with caching enabled, as it may impact performance.