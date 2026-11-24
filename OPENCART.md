# OpenCart installation and release notes

Published by **X PAY LAB LIMITED**. Packaged version instructions follow.

```text
XPay x402 USDC Payments for OpenCart — 1.0.1
Publisher: X PAY LAB LIMITED | https://x-pay.llc/ | chris@x-pay.llc

TARGET
OpenCart 4.1.0.4, PHP 8.1+ (64-bit), MySQL/MariaDB, cURL, OpenSSL.
Requires an HTTPS storefront, USD store base and checkout currency, JavaScript,
a compatible injected EVM browser wallet, and an XPay merchant account/API key.
Base mainnet USDC only. No recurring payments. Other currencies, chains, OpenCart
3 and Cloud Marketplace compatibility are not supported by this release.
The project token $XPAY is not needed for checkout.

INSTALL
1. Back up the store. Test on staging before enabling live payments.
2. In Extensions > Installer upload xpay.ocmod.zip, then click Install.
3. In Extensions > Extensions choose Payments, install XPay, then Edit.
4. Set USD as the store base currency and checkout currency. Configure the store
   encryption key. Enter your Base receiving address. Leave Test simulation Yes
   for testing. Choose the order status to use after verified live payment.
5. Run a simulation checkout. It must say no tokens moved and must NOT mark the
   order paid. Use only synthetic customer data in tests.
6. Obtain your XPay merchant key separately. For live payments enter the key,
   the merchant-approved HTTPS facilitator endpoint, and Base RPC endpoint.
   The default facilitator is https://facilitator-xpay.llc and the public RPC is
   https://mainnet.base.org. Confirm facilitator API access before enabling live.
   Disable simulation only after staging review and a merchant-controlled live
   test. This release has no hosted testnet facility.

HOW IT WORKS
OpenCart creates its normal unconfirmed order at checkout. XPay binds the final
USD total (1 USD checkout unit to 1 USDC) to a short-lived Base USDC authorization.
The shopper reviews and signs in their own wallet. The server validates the
cart, stock, order, recipient, amount, currency, session and authorization.
The server calls the configured facilitator's /verify and /settle API and checks
Base transaction receipt logs for the exact USDC transfer and authorization
nonce. Only verified live settlement can advance the order to the selected
status. Stablecoin value can vary against USD; no exchange-rate conversion or
separate plugin-added fee is performed.

FEES AND EXTERNAL SERVICES
The module download is free. Live processing requires an XPay merchant account;
processing/service fees are governed by the merchant's XPay agreement. Confirm
those terms before use. The module does not itself add or deduct a separate
processing fee. Network/facilitator charges may apply under that agreement.
Payment authorization, payer/recipient addresses, amount, asset, chain and nonce
are sent to the configured facilitator. RPC requests reveal transaction hashes.
Order contents, email, name and postal address are not sent by this module.
No wallet private keys are requested or stored. Merchant API keys use OpenCart's
server-side settings storage and are masked in the admin UI. Protect database
backups and restrict admin access. Payment snapshots/authorizations are encrypted
with authenticated encryption using the store encryption key; keep it backed up.

RECONCILIATION
Once a signature is submitted, do not pay again. Click Recheck payment. The same
nonce and idempotency key are reused. If the provider's settlement response is
lost, rechecking depends on its idempotency support. Contact the merchant/XPay
if the status stays pending. A pending transfer may settle even if checkout is
closed. Do not delete the order or rotate the store encryption key while a
payment is unresolved. Database table PREFIX_xpay_payment retains encrypted
records and unique transaction hashes for reconciliation. Uninstall retains
this financial evidence. A changed order after payment requires manual review.
Do not manually mark unpaid orders paid just because a signature exists.
Refunds must be arranged by the merchant; automatic wallet refunds are not
implemented. Simulation leaves orders unconfirmed; it is not a payment.

SECURITY / LIMITS
Only store administrators can change payment settings. Endpoints are HTTPS,
publicly resolved and bounded; redirects/private addresses are rejected.
Use a reliable Base RPC and a least-privilege XPay merchant key. Never put a
wallet private key in the key field. Keep OpenCart and PHP security updates.
This module verifies successful receipt logs; it does not wait for Base L1
finality. Merchants should apply suitable fulfillment/reorganization policies.
Custom checkout/total/stock extensions and multi-currency configurations require
separate compatibility review. See validation report for checks actually run;
no real funds were moved during automated tests.

SUPPORT
chris@x-pay.llc | https://x-pay.llc/ | https://github.com/xpayllc
Report security issues privately. Never send private keys or full database dumps.

1.0.1 CHANGES
Branded responsive payment panel, clear USDC total and receiving wallet; pending
orders remain protected across page reloads. Service errors display a safe code
and support reference while detailed responses remain encrypted on the server.
Transaction hashes in failure responses are retained for receipt review.

```
