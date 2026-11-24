# Bagisto installation and release notes

Published by **X PAY LAB LIMITED**. Packaged version instructions follow.

# XPay USDC payments for Bagisto

Version 0.3.1. Minimum Bagisto version: **2.5.1**. PHP **8.4+**, Laravel **13**, HTTPS and a shared Laravel atomic-lock cache are required. Tested with MySQL, MariaDB and PostgreSQL. Base mainnet is supported. BNB Chain is displayed as **Coming soon** and cannot be selected or called in this release.

## Installation

1. Back up your store and database. Extract the zip and merge the `packages` folder into the Bagisto root directory. The extension will be at `packages/XPay/Bagisto/`. Keep the accompanying README, CHANGELOG and LICENSE for reference.
2. Add a Composer path repository without replacing existing repositories:

```json
{"type":"path","url":"packages/XPay/Bagisto","options":{"symlink":false}}
```

3. From the Bagisto root:

```sh
composer require xpayllc/bagisto-xpay:0.3.1
php artisan migrate --force
php artisan vendor:publish --tag=xpay-assets --force
php artisan optimize:clear
```

Composer declares a conflict with Bagisto versions below 2.5.1. If an untagged source checkout reports `1.0.0+no-version-set`, first verify that its actual release is at least 2.5.1; then use the correct `COMPOSER_ROOT_VERSION` for that checkout. Do not mislabel an older Bagisto version. Laravel package discovery loads the provider. If discovery is disabled, register `XPay\Bagisto\Providers\XPayServiceProvider::class` in `bootstrap/providers.php`.

## Configure each channel in the admin panel

Configure → Sales → Payment Methods → XPay USDC · Base:

- Recipient wallet: the merchant's Base receiving address.
- Facilitator access key: the dedicated Facilitator Access key from the XPay dashboard.
- Facilitator HTTPS endpoint: `https://facilitator-xpay.llc` (no `/verify` or `/settle` suffix).
- Base HTTPS RPC URL: your trusted Base-mainnet RPC URL, for example `https://mainnet.base.org`.
- Title, description, sort order and enabled state.
- Simulated test mode: leave enabled for initial testing; disable before accepting live payments.

Recipient is required when enabled. Live key, endpoint and RPC fields are required unless simulated test mode is enabled. Dependent fields appear only when the method is enabled. The facilitator endpoint is a text field. The facilitator key is masked. The RPC URL remains masked because provider URLs can contain API credentials. All fields are channel-specific; Gateway reads them through `getConfigData()`. Shared XPay validators handle wallet addresses and conditional live credentials in both the admin browser and server. They are never included in the checkout page or logs. No `.env` payment credentials are used. Protect database backups and administrator access. Authorization and the immutable order snapshot are encrypted with Laravel's APP_KEY, which must remain stable.

Set both the channel base currency and checkout currency to **USD**. The merchant receives exactly the corresponding native USDC amount on Base. No exchange conversion is implemented. A channel whose base currency is EUR cannot enable XPay by switching the customer's display currency to USD. Do not create a four-letter USDC store currency: Bagisto accepts three-letter currency codes.

## Simulated test mode

This is a clearly labelled local/store simulation, **not a Base testnet transaction**. It uses no wallet signatures, facilitator requests, RPC requests or funds. Click “Simulate test payment” to exercise checkout and order creation. Test orders and merchant transaction records contain `mode=sandbox` and a `sandbox_` reference; no blockchain hash is fabricated. Never ship goods for these orders. A simulated payload is rejected in live mode, and an intent cannot cross modes after creation. Resolve outstanding live intents before changing a channel's mode, credentials or receiving wallet.

## Checkout and order lifecycle

GET and HEAD `/xpay/checkout/base` render a read-only entry form. Clicking Continue with XPay sends a CSRF-protected POST which creates only an encrypted XPay intent and immutable order snapshot. No Bagisto order, invoice, order-stock reservation or new-order email is created. The stored snapshot is read back and decrypted before wallet checkout is shown. The unsigned cart stays active, so closing the tab does not lose it. The CSRF-protected Cancel action ends an unsigned intent. Expiry also restores an inactive legacy unsigned cart. The cart is deactivated when a verified authorization is persisted or a simulated order is completed.

After the facilitator verifies/settles the exact authorization, the extension independently checks the Base receipt, native USDC contract, payer, recipient, amount and order-bound authorization nonce. Only then is the Bagisto order created and invoiced. Repeated requests cannot create another invoice or reuse a settlement transaction for a second intent. No XPay order exists for an unsigned quote to ship or invoice prematurely.

Inventory and coupon eligibility are rechecked, totals are recalculated, and current checkout details must still match the persisted snapshot immediately before settlement. Inventory is not reserved before payment; another purchase can still change stock between that check and final order creation. If inventory or order creation fails after a verified payment, the verified transaction evidence remains saved and the intent is flagged for merchant review. Background retries stop for these review cases; an operator must explicitly reconcile the verified hash after resolving the issue, or handle the refund separately. Do not request a second payment. The merchant must complete the existing order or separately authorize a refund. Automatic refunds are not implemented.

Legacy v0.2.2 orders can be reconciled to an invoice the administrator has already created without creating a duplicate invoice. Canceled, closed, fraud-marked, changed or otherwise conflicting legacy orders are not fulfilled automatically.

## Automatic confirmation and expiry

Configure the standard Laravel scheduler on **one scheduler node**:

```cron
* * * * * cd /path/to/bagisto && php artisan schedule:run >> /dev/null 2>&1
```

The package registers `xpay:sync` every minute with overlap prevention. Check it with `php artisan schedule:list`. A persistent shared cache that supports atomic locks (database or Redis is recommended) must be used across application nodes; the `array` cache is test-only. Locks no longer depend on the SQL driver. Allow the PHP process enough time to finish an in-flight provider request.

The job checks persisted authorizations/hashes without requiring the customer's browser. A stored hash is checked without broadcasting a new transaction. An authorization without a known hash can retry the identical Base settlement with the original idempotency key; it never obtains a new signature. Expired unsigned intents are marked expired; pre-upgrade unpaid orders are canceled through Bagisto's repository to release reserved stock. Submitted authorizations are not discarded just because a quote expires, since settlement may already have occurred. Cancellation is blocked while payment is unresolved.

You can also run:

```sh
php artisan xpay:sync --limit=100
php artisan xpay:reconcile INTENT_ID TRANSACTION_HASH
```

The manual command validates an already-known receipt and never broadcasts. The scheduler processes oldest-checked intents first with bounded batches. A provider outage, missing original transaction hash or failed receipt check remains pending for recovery and merchant review. Logs record intent/order IDs, operation stage and exception class, without tokens, signatures, request payloads or secret URLs.

## Agent requests

Use the ordinary authorized cart/checkout flow. Keep the session cookie and CSRF token. GET `/xpay/payment` returns x402 v2 requirements with the required `extensions.xpay-order.nonce`. Sign that exact nonce and amount, then POST a `paymentPayload` or `PAYMENT-SIGNATURE` with `X-CSRF-TOKEN`. HTTP 200 with `paid:true` is confirmed; HTTP 202 is unresolved. Do not sign another authorization to retry. GET and HEAD never settle a payment. The optional X-XPay-Order-Key header does not replace checkout-session ownership.

## Translations and assets

Admin labels, checkout text, browser messages and customer-facing validation errors use the `xpay::app` catalogue loaded from `Resources/lang`. English and a Dutch catalogue are included; untranslated strings use Laravel's configured English fallback. Additional Bagisto locales can supply the same keys without editing controllers or JavaScript. The package does not claim completed native translations for all 23 Bagisto locales.

The official XPay logo, 512×512 listing icon and 1600×900 banner are included. All checkout assets are local. No remote tracking script or private key is included.

## Validation

See the separately supplied Review Notes and Regression Tests archive. Review-only material is deliberately excluded from this installable package. Version 0.3.1 has automated framework tests on Bagisto **2.5.1**, PHP 8.4 and Laravel 13, plus browser-flow and payment-rule tests. External payment calls are doubled in the automated tests. No new live-money acceptance test is claimed for v0.3.1. This release does not include a hosted Base Sepolia facilitator, testnet credentials or a faucet workflow. BNB has not been enabled or represented as tested live in this release.

No third-party vendor dependencies are bundled. The store owner remains responsible for updates to the Bagisto/Laravel host and for confirming commercial facilitator fees. The extension does not separately deduct the advertised XPay fee, invent activity or automatically refund funds.

## Upgrade notes

The 0.3.1 migration expands encrypted order snapshots to LONGTEXT on MySQL/MariaDB (TEXT on PostgreSQL). It preserves existing records but cannot reconstruct data already truncated by a previous version. Cancel/recreate a corrupt unsigned checkout; retain and investigate any submitted payment evidence. Migrations preserve existing payment evidence, make `order_id` nullable, add a restrictive foreign key to `orders`, allow a canceled/expired cart to retry, and add encrypted order snapshots and mode/reference metadata. Existing orders must not be deleted to clear payment problems. Database downgrade is intentionally blocked; restore the pre-upgrade backup rather than dropping evidence. Reconfigure channel credentials in the admin panel after upgrading from environment-based 0.2.2.

Support: chris@x-pay.llc
Website: https://x-pay.llc/
Facilitator: https://facilitator-xpay.llc/
Dashboard: https://www.app-xpay.com/
Documentation: https://www.api-xpay.com/api/docs/
Twitter/X: https://x.com/xpayllc
