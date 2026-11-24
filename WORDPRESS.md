# WordPress & WooCommerce installation and release notes

Published by **X PAY LAB LIMITED**. Packaged version instructions follow.

```text
=== X PAY LAB USDC Payments for WooCommerce ===
Contributors: xpayllc
Tags: woocommerce, payments, usdc, x402, base
Requires at least: 6.5
Tested up to: 7.1
Requires PHP: 8.0
Stable tag: 0.1.3
License: GPLv2 or later
License URI: https://www.gnu.org/licenses/gpl-2.0.html

Accept Base USDC with an order-bound x402 payment flow.

== Description ==
An official XPay integration from X PAY LAB LIMITED (British Virgin Islands company 2221444), the operator of https://x-pay.llc/ and https://facilitator-xpay.llc/. This plugin is unrelated to Nexi XPay, xpay.app, xpay.sh and other similarly named services. It is an independent integration for WooCommerce, the x402 protocol and USDC; it does not imply endorsement by WooCommerce, Coinbase, Base or Circle.

Accept Base USDC for WooCommerce orders through XPay. Validate real-wallet settlement and your hosting configuration on staging before accepting live customer orders. A working XPay facilitator merchant API credential is required.

Includes classic checkout, Checkout Blocks registration, injected browser-wallet checkout, agent order-payment API, pending-payment reconciliation and independent Base receipt checks. No private keys are requested. No automatic refunds or recurring charges. Base mainnet USDC only; USD and USDC store currencies only. USD totals are quoted 1:1 in USDC; no exchange-rate conversion. No WalletConnect or automatic chain registration in this release. Agents must support the documented order-bound nonce extension; generic x402 clients may need an adapter.

This plugin does not create orders for agents. Orders must first be created using the store's existing authenticated checkout/order flow, including customer consent, inventory, shipping and tax. It does not monetize arbitrary WordPress posts or implement subscription billing.

== Installation ==
1. Install WooCommerce on a staging WordPress website using HTTPS and 64-bit PHP 8+.
2. Upload xpay-lab-usdc-payments-0.1.3.zip under Plugins > Add New > Upload Plugin and activate.
3. Open WooCommerce > Settings > Payments > XPay USDC.
4. Enter a Base receiving address and XPay facilitator API key. Prefer XPAY_FACILITATOR_KEY in wp-config.php instead of storing the credential in WordPress settings.
5. Enable XPay after verifying your account is permitted to settle to that receiving wallet.
6. Complete the acceptance checklist in docs/INTEGRATION.md before production use.

== External services ==
This plugin depends on XPay Facilitator and the Base public RPC. It makes no external requests on activation or merely viewing a product. The merchant must configure and enable the gateway; the buyer must select XPay and authorize the payment before settlement. An authorized payment agent may also submit a signed authorization for an existing order.

= XPay Facilitator (X PAY LAB LIMITED) =
Service: https://facilitator-xpay.llc/verify and https://facilitator-xpay.llc/settle. Verification checks a signed USDC authorization; settlement submits the authorized transfer on Base. When a customer submits a payment, the store sends the payer and merchant public wallet addresses, USDC amount, Base network/token, authorization nonce and validity timestamps, signature and resource metadata (the order payment endpoint URL and order number). The store sends the merchant API credential in an Authorization header and an order ID/nonce in the Idempotency-Key header. A pending-payment recheck resends the same stored authorization to /settle. The service also receives the store server's IP address as part of the connection. The plugin does not send billing/shipping addresses, names, email addresses or wallet private keys.

XPay privacy policy: https://x-pay.llc/legal/#privacy
XPay published terms of use: https://x-pay.llc/legal/#terms
Service documentation: https://facilitator-xpay.llc/
The linked public terms describe website use and refer payment integrations to the applicable merchant service agreement. Obtain that agreement from support@x-pay.llc before enabling live payments; the plugin does not supply or replace it. The extension is free to download. XPay's stated merchant processing fee is 1%; confirm applicable charges in your merchant agreement.

= Base public RPC (Coinbase/Base) =
Service: https://mainnet.base.org. After the facilitator reports a settlement transaction, the store sends its public transaction hash in an eth_getTransactionReceipt JSON-RPC request to independently check successful execution and the expected merchant USDC transfer. Reconciliation can repeat this lookup. The provider receives the store server's IP address; the returned public receipt contains wallet addresses, amounts and logs. No XPay API credential, signed payment authorization, customer contact/address data or private wallet key is sent to this RPC by the plugin. The public endpoint can be rate-limited and has no availability guarantee for a production store.

Base terms of service: https://docs.base.org/terms-of-service
Base privacy policy: https://docs.base.org/privacy-policy

= Connected wallet =
After the customer presses Connect wallet and pay, the checkout asks their installed Ethereum-compatible wallet for a public account address, switches it to Base and requests a signature of the displayed merchant address, amount, asset, chain and authorization validity/nonce. The wallet is chosen by the customer, not supplied or remotely loaded by this plugin. Its provider may operate its own RPC and privacy practices; the customer should review that wallet's terms. A signature authorizes a real USDC payment. Never enter a seed phrase or private key into the store.

== Privacy ==
Stores the order quote, transaction receipt and temporarily the signed payment authorization in WooCommerce order metadata. It sends no wallet private keys. Order access keys permit checking/paying that order: do not publish them. Order metadata and API credentials should be covered by the merchant's retention, backup and access policies. Deactivation retains receipts for bookkeeping. No remote executable code or analytics is loaded.

== Changelog ==
= 0.1.3 =
Use the company-specific X PAY LAB display name and slug. Clarify operator identity, independent integration status, external-service data flows, provider terms/privacy links and processing fees. Payment processing behavior is unchanged.

= 0.1.2 =
Initial release. No WordPress.org or WooCommerce Marketplace approval is claimed.

```
