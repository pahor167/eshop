# Shopify Admin API Access Scopes Reference

Complete list of authenticated access scopes for the Admin API.

## Products & Inventory

| Scope | Access |
|-------|--------|
| `read_products`, `write_products` | Products, variants, collections |
| `read_inventory`, `write_inventory` | Inventory levels and items |
| `read_product_listings`, `write_product_listings` | Product/collection listings |

## Orders & Fulfillment

| Scope | Access |
|-------|--------|
| `read_orders`, `write_orders` | Orders, transactions, fulfillments, abandoned checkouts |
| `read_all_orders` | All orders (not just last 60 days) - **requires permission** |
| `read_draft_orders`, `write_draft_orders` | Draft orders |
| `read_fulfillments`, `write_fulfillments` | Fulfillment services |
| `read_assigned_fulfillment_orders`, `write_assigned_fulfillment_orders` | Fulfillment orders |

## Customers

| Scope | Access |
|-------|--------|
| `read_customers`, `write_customers` | Customers, segments, companies |
| `read_customer_merge`, `write_customer_merge` | Customer profile merges |

## Discounts & Pricing

| Scope | Access |
|-------|--------|
| `read_discounts`, `write_discounts` | Discount features |
| `read_price_rules`, `write_price_rules` | Price rules |

## Content & Themes

| Scope | Access |
|-------|--------|
| `read_content`, `write_content` | Articles, blogs, comments, pages |
| `read_themes`, `write_themes` | Theme templates and assets |
| `read_online_store_navigation`, `write_online_store_navigation` | URL redirects |
| `read_online_store_pages` | Online Store pages |

## Files & Media

| Scope | Access |
|-------|--------|
| `read_files`, `write_files` | Generic files |

## Metafields & Metaobjects

| Scope | Access |
|-------|--------|
| `read_metaobjects`, `write_metaobjects` | Metaobject entries |
| `read_metaobject_definitions`, `write_metaobject_definitions` | Metaobject definitions |

## Locations & Shipping

| Scope | Access |
|-------|--------|
| `read_locations`, `write_locations` | Store locations |
| `read_shipping`, `write_shipping` | Delivery carrier services |

## Other

| Scope | Access |
|-------|--------|
| `read_locales`, `write_locales` | Shop locales |
| `read_translations`, `write_translations` | Translatable resources |
| `read_markets`, `write_markets` | Markets |
| `read_gift_cards`, `write_gift_cards` | Gift cards |
| `read_reports` | Analytics via shopifyqlQuery |

## Scopes Requiring Special Permission

These require approval from Partner Dashboard before use:

- `read_all_orders` - Access all orders (not just 60 days)
- `read_customer_payment_methods` - Customer payment methods
- `read_own_subscription_contracts`, `write_own_subscription_contracts` - Subscriptions

## Quick Copy: Common Combinations

**Products only:**
```
read_products, write_products
```

**Products + Inventory:**
```
read_products, write_products, read_inventory, write_inventory, read_locations
```

**Full store management:**
```
read_products, write_products, read_orders, write_orders, read_customers, write_customers, read_inventory, write_inventory, read_fulfillments, write_fulfillments, read_draft_orders, write_draft_orders, read_locations, read_price_rules, write_price_rules, read_discounts, write_discounts, read_content, write_content, read_themes, write_themes, read_files, write_files, read_metaobjects, write_metaobjects, read_metaobject_definitions, write_metaobject_definitions, read_online_store_navigation, write_online_store_navigation
```
