=== PayPal Framework ===
Contributors: aaroncampbell
Donate link: https://www.paypal.com/cgi-bin/webscr?cmd=_xclick&business=paypal%40xavisys%2ecom&item_name=PayPal%20Framework&no_shipping=0&no_note=1&tax=0&currency_code=USD&lc=US&bn=PP%2dDonationsBF&charset=UTF%2d8
Tags: paypal
Requires at least: 2.8
Tested up to: 4.4.2
Stable tag: 1.0.12

PayPal integration framework and admin interface as well as IPN listener.

== Description ==

This plugins gives you a great framework to use for integrating with PayPal.
It stores both live and sandbox API credentials and allows you to switch back
and forth easily.  All NVP API calls are passed through the framework and
default values such as API version, API credentials, and even currency code are
added to the request based on settings from the admin panel.

It also has a built in IPN listener that validates messages as coming from
PayPal then throws WordPress actions based on messages received.  For example it
throws "paypal-recurring_payment_profile_cancel" when someone cancels a
recurring payment that they had set up with you.  It passes along all the info
that PayPal sent to the action, so it's simple to create other plugins that use
this one.

Requires PHP5.

You may also be interested in WordPress tips and tricks at <a href="http://wpinformer.com">WordPress Informer</a> or gerneral <a href="http://webdevnews.net">Web Developer News</a>

== Installation ==

1. Use automatic installer.

== Frequently Asked Questions ==

= How do I send a request to PayPal? =

To send a request to PayPal, simply build the request as an associative array and pass it to the hashCall helper function like this:
<code>
$ppParams = array(
	'METHOD'         => 'doDirectPayment',
	'PAYMENTACTION'  => 'Sale',
	'IPADDRESS'      => '123.123.123.123',
	'AMT'            => '222.22',
	'DESC'           => 'some product',
	'CREDITCARDTYPE' => 'VISA',
	'ACCT'           => '4111111111111111',
	'EXPDATE'        => '112011',
	'CVV2'           => '123',
	'FIRSTNAME'      => 'Aaron',
	'LASTNAME'       => 'Campbell',
	'EMAIL'          => 'pptest@xavisys.com',
	'STREET'         => '123 some pl',
	'STREET2'        => '',
	'CITY'           => 'San Diego',
	'STATE'          => 'CA',
	'ZIP'            => '92101',
	'COUNTRYCODE'    => 'US',
	'INVNUM'         => '12345',
);

$response = hashCall($ppParams);
</code>

= How do I use the Listener to process PayPal messages? =

First you have to tell PayPal to send message to the correct URL.  Go to the
PayPal Framework settings page and click the "PayPal IPN Listener URL" link to
see instructions on how to use the URL that's listed there.  Once your PayPal
account has been set up the listener will automatically process all IPN messages
and turn them into WordPress actions that you can hook into.  You can use the
'paypal-ipn' action to look at every message you ever get, or hook directly into
a 'paypal-{transaction-type}' hook to process a specific type of message:
<code>
add_action('paypal-ipn', 'my_process_ipn');
function my_process_ipn( $data ) {
	echo 'Processing IPN Message:<pre>';
	var_dump( $data );
	echo '</pre>';
}

add_action('paypal-recurring_payment_failed', 'my_process_ipn_recurring_payment_failed');
function my_process_ipn_recurring_payment_failed( $data ) {
	echo 'A recurring payment has failed. Here is the data I have to process this:<pre>';
	var_dump( $data );
	echo '</pre>';
}
</code>

= Why do you set sslverify to false? =

Many servers have out of date certificate lists, so this is necessary to be as
portable as possible.  However, if your server is set up right you can force
sslverify like this:
<code>
add_filter( 'paypal_framework_sslverify', '__return_true' );
</code>

== Changelog ==

= 1.0.12 =
* Add httpversion 1.1 to wp_remote_get(). Now required by PayPal Sandbox to support TLS 1.2. - props @cferdinandi

= 1.0.11 =
* Don't verify SSL on validation calls - too many people with out of date CAs

= 1.0.10 =
* Replace instance of _() with __() - Props Ken Bass

= 1.0.9 =
* Use admin-ajax for listener
* Add sslverify filter - props @evansolomon

= 1.0.8 =
* Fix missing quotes

= 1.0.7 =
* Lots of code cleanup, some requiring WordPress 2.8+
* Better help
* Make translatable

= 1.0.6 =
* Fixed a bug that throws a warning for certain requests when in debugging mode.  Props Ken Bass <kbass@kenbass.com>

= 1.0.5 =
* Fixed a bug introduced in 1.0.4 that affected certain debug messages when not using the sandbox

= 1.0.4 =
* Debug E-Mails for live requests now get an obfuscated credit card number (ACCT) as well as EXPDATE and CVV2
* The IPN listener only throws a transaction-specific action if a txt_type is given in the message

= 1.0.3 =
* IPN Message validations now work even if there are apostophes (slashes are stripped)
* You can now have multiple debug E-Mail addresses (comma separated)

= 1.0.2 =
* Added a / to the IPN URL so that PayPal doesn't complain that it's invalid
* Fixed a couple debug messages to send the proper URL used
* Added a more general "paypal-ipn" action that can be used to catch and process all IPN message
* Moved add_action calls inside the __construct

= 1.0.1 =
* Added sendToExpressCheckout method for sending users to PayPal to finish up Express Checkout Payments
* Changed hashCall to use the WordPress WP_Http class
* Changed makeNVP to a public method
* Updated makeNVP to use http_build_query
* Switched to using wp_remote_post rather than specifying POST and using wp_remote_request

= 1.0.0 =
* Original version released to wordpress.org repository