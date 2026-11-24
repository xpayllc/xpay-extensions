<p align="center"><img src="xpay-logo.png" alt="XPay" width="160"></p>

# XPay extensions

Official store integrations published by **X PAY LAB LIMITED**.

Accept USDC on Base through XPay with extensions for WordPress and WooCommerce, OpenCart, and Bagisto. Choose your platform below to download an installable package and follow its setup guide.

[Website](https://x-pay.llc/) · [Merchant dashboard](https://www.app-xpay.com/) · [Facilitator](https://facilitator-xpay.llc/) · [Support](mailto:chris@x-pay.llc)

## Downloads

| Platform | Version | Installable package | Setup | Official listing |
| --- | --- | --- | --- | --- |
| WordPress / WooCommerce | 0.1.3 | [Download plugin](https://github.com/xpayllc/xpay-extensions/raw/refs/heads/main/xpay-lab-usdc-payments-0.1.3.zip) | [WordPress guide](WORDPRESS.md) | [WordPress.org](https://wordpress.org/plugins/xpay-lab-usdc-payments/) |
| OpenCart | 1.0.1 | [Download extension](https://github.com/xpayllc/xpay-extensions/raw/refs/heads/main/xpay.ocmod.zip) | [OpenCart guide](OPENCART.md) | [OpenCart Marketplace](https://www.opencart.com/index.php?route=marketplace/extension/info&extension_id=50632) |
| Bagisto | 0.3.1 | [Download extension](https://github.com/xpayllc/xpay-extensions/raw/refs/heads/main/XPay-Bagisto-0.3.1.zip) | [Bagisto guide](BAGISTO.md) | [Bagisto Extensions](https://bagisto.com/en/extensions/xpay-x402-usdc-base/) |

Download the individual platform package above. GitHub's **Code → Download ZIP** downloads this whole collection and is not a platform installer.

## Compatibility

| Integration | Requirements |
| --- | --- |
| WordPress / WooCommerce | WordPress 6.5+, WooCommerce, 64-bit PHP 8.0+, HTTPS. USD or USDC store currency. |
| OpenCart | OpenCart 4.1.0.4, 64-bit PHP 8.1+, MySQL/MariaDB, cURL, OpenSSL, HTTPS. USD base and checkout currency. |
| Bagisto | Bagisto 2.5.1+, PHP 8.4+, Laravel 13, HTTPS, shared atomic-lock cache and Laravel scheduler. USD base and checkout currency. |

Each package includes its source code and original license. Bagisto also has a [dedicated distribution repository](https://github.com/xpayllc/bagisto-xpay).

## Getting started

1. Download the package for your store and verify it against [SHA256SUMS.txt](SHA256SUMS.txt).
2. Back up the store and install on staging using the platform guide.
3. Configure the merchant receiving wallet and the required dedicated XPay facilitator credential. Keep credentials on the server.
4. Verify the payment workflow before enabling live checkout. A wallet signature alone is not payment confirmation.

These releases support **native USDC on Base mainnet**. USD totals are quoted 1:1 in USDC; no foreign-exchange conversion is provided. **BNB Chain USDC and KRW payments are coming soon and are not enabled by these packages.** The XPAY project token is not required for checkout.

## Fees and payment handling

The extensions are free to download. XPay's stated merchant processing fee is 1%; confirm applicable processing, network and service charges in your merchant agreement. The packages do not separately implement a new fee deduction.

Merchants need a receiving wallet and permitted facilitator access. Pending or uncertain payments must be reconciled before fulfillment. Automatic wallet refunds and recurring payments are not included. Simulation, where available, does not move funds. Automated checks do not establish live-money acceptance; complete staging and merchant-controlled payment validation for your own store.

## Release integrity and licenses

The installable ZIP files are preserved exactly as distributed. [SHA256 checksums](SHA256SUMS.txt) identify the archived versions. Installation guides reproduce the corresponding package instructions; the WordPress package contains a GPLv2-or-later license. Refer to each archive's included license and notices for its terms. This collection does not replace those licenses.

## Support

Published by **XPay — X PAY LAB LIMITED**, British Virgin Islands.

For installation help, contact [chris@x-pay.llc](mailto:chris@x-pay.llc). Report security issues privately. Do not include merchant credentials, signed payment authorizations, order access keys, seed phrases or private keys in public issues.
