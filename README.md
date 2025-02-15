<p align="center"><img src="./src/icon.svg" width="100" height="100" alt="PayPal Checkout for Craft Commerce icon"></p>
<h1 align="center">PayPal Checkout for Craft Commerce</h1>

This plugin provides a [PayPal Checkout](https://www.paypal.com/uk/webapps/mpp/checkout) gateway integration for [Craft Commerce](https://craftcms.com/commerce).

## Requirements

This plugin requires Craft 4.0 and Craft Commerce 4.0 or later.

## Installation

You can install this plugin from the Plugin Store or with Composer.

#### From the Plugin Store

Go to the Plugin Store in your project’s Control Panel and search for “PayPal Checkout for Craft Commerce”. Then click **Install** in the plugin’s modal window.

#### With Composer

Open your terminal and run the following commands:

```bash
# go to the project directory
cd /path/to/my-project.test

# tell Composer to load the plugin
composer require craftcms/commerce-paypal-checkout

# tell Craft to install the plugin
php craft plugin/install commerce-paypal-checkout
```

## Setup

### Creating PayPal REST API Credentials

The following steps are from the [PayPal guide](https://www.paypal.com/us/smarthelp/article/how-do-i-create-rest-api-credentials-ts1949) on how to create REST API credentials. 

REST API credentials include a client ID and secret. Here's how you generate the credentials:

1. Log in to the [PayPal Developer Portal](https://developer.paypal.com/) using the same credentials you use for [PayPal](https://paypal.com/).
2. On the **My Apps & Credentials** page, click **Live** or **Sandbox** depending on whether you need an app for testing (Sandbox) or going live (Live).
3. Click **Create App** under **REST API** apps. Any previously created REST API apps will appear in the table above the **Create App** button.
4. Enter the name of your REST API app in the **App Name** field, and select a Sandbox business account to associate with your app.
**Note:** Remember that you can't use a Live credit card in Sandbox, and you can't use a test credit card in your Live account.
5. Click **Create App**.
6. Your credentials, the client ID and secret, are displayed on the app details page that displays after you click **Create App**.
7. Request permissions for REST API features that apply to your integration:
    - PayPal payments

You will now be able to see the **client ID** and **secret** for your newly created app.

When you are ready to take your code live, make sure you create a Live app to get live credentials.

Use the **Live** and **Sandbox** toggle at the top of **My Apps & Credentials** to switch between app types and view your credentials for each.

### Creating PayPal Checkout Gateway in Commerce

To add the PayPal Checkout gateway, go to **Commerce** → **Settings** → **Gateways**, create a new gateway, and set the gateway type to “PayPal Checkout”.

In the gateway settings, enter the **client ID** and **secret** for your REST app in their respective fields.

### Cart Info / Cost Breakdown

Turning on the **Send Cart Information** setting for this gateway will send cart items and cost breakdown through to PayPal.

**Note:** If you’re using deprecated custom adjuster types, this setting will need be turned off.

### Paying with Non-Primary Currency

When using the `getPaymentFormHtml()` method (e.g. `cart.gateway.getPaymentFormHtml({})`) and allowing payment in a currency other than the primary currency, you must pass the currency ISO in the method params.

For example, if you’ve already set the alternative payment currency on the cart you could do the following:

```twig
{{ cart.gateway.getPaymentFormHtml({
  currency: cart.paymentCurrency
}) }}
```

This is required when paying with an alternative payment currency due to the integration of the PayPal JavaScript SDK.

### SDK Query String Parameters

This gateway allows passing specific query string parameters at runtime, as specified in the [PayPal documentation](https://developer.paypal.com/docs/checkout/reference/customize-sdk/#query-parameters).

The allowed parameters are `currency` (as detailed above), `disable-card`, `disable-funding` and `locale`.

For example, to disable PayPal credit funding you could output the payment form as follows:

```twig
{{ cart.gateway.getPaymentFormHtml({
  ...
  'disable-funding': 'credit'
  ...
}) }}
```

### Use custom payment urls

If custom payment urls are needed, they can be provided by using `prepareUrl` and `completeUrl`. One common use-case for that, is setting the used site.

```cart
{% namespace cart.gateway.handle|commercePaymentFormNamespace %}
  {{ cart.gateway.getPaymentFormHtml({
    currency: cart.paymentCurrency,
    prepareUrl: actionUrl('commerce/payments/pay', {'siteToken': currentSite.id|hash }),
    completeUrl: actionUrl('commerce/payments/complete-payment', {'siteToken': currentSite.id|hash }),
  })|raw }}
{% endnamespace %}
```
