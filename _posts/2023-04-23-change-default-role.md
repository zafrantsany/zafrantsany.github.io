---
layout: post
title: How To Change Default Role on Wordpress and Woocommerce
author: Zafran Tsany
tags: [overview, moonwalk]
category: [wordpress, woocommerce]
---

To achieve this, you can use custom code in your theme's functions.php file or create a custom plugin. Here's a step-by-step guide to change the default user role from "Customer" to "Subscriber" when a user logs in through the WooCommerce login form, and set it to "Customer" only when a user purchases a product:

1. Open your theme's functions.php file, which is located in wp-content/themes/your-theme/. If you don't have a child theme, it's recommended to create one to prevent losing custom code when the theme updates.
2. Add the following code to change the default user role when a user registers using the WooCommerce login form:

```php
// Change default role for WooCommerce registration
function custom_woocommerce_registration_default_role( $args ) {
    $args['role'] = 'subscriber';
    return $args;
}
add_filter( 'woocommerce_new_customer_args', 'custom_woocommerce_registration_default_role', 10, 1 );
```

3. Add the following code to change the user role to "Customer" when a user purchases a product:

```php
// Change user role to 'Customer' after a successful purchase
function custom_change_role_after_purchase( $order_id ) {
    $order = wc_get_order( $order_id );
    $user = $order->get_user();
  
    if ( $user && 'subscriber' === $user->roles[0] ) {
        $user->set_role( 'customer' );
    }
}
add_action( 'woocommerce_order_status_completed', 'custom_change_role_after_purchase', 10, 1 );
```

This code will change the user role to "Customer" when an order is marked as completed. If you want to change the user role at a different stage in the order process, you can replace 'woocommerce_order_status_completed' with another WooCommerce order status action hook, such as 'woocommerce_order_status_processing' or 'woocommerce_order_status_on-hold'.

4. Save the functions.php file and test your changes on your website. New users should now be assigned the "Subscriber" role when they register using the WooCommerce login form, and their role should be changed to "Customer" when they make a purchase.

Remember that modifying the functions.php file directly in the parent theme is not recommended, as it can lead to losing your customizations when updating the theme. Always use a child theme or create a custom plugin for such customizations.
