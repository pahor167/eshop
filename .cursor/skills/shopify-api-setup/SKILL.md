---
name: shopify-api-setup
description: Set up Shopify Admin API access using Dev Dashboard and Client Credentials Grant. Use when setting up Shopify API credentials, generating access tokens, configuring access scopes, or troubleshooting API authentication errors.
---

# Shopify Admin API Setup

Guide for setting up local Shopify Admin API access using the Dev Dashboard and Client Credentials Grant flow.

## Prerequisites

- Access to [dev.shopify.com/dashboard](https://dev.shopify.com/dashboard)
- A Shopify store to connect to

## Setup Workflow

### Step 1: Create App in Dev Dashboard

1. Go to [dev.shopify.com/dashboard](https://dev.shopify.com/dashboard)
   - Or from Shopify Admin: click store name (top right) → **Dev Dashboard**
2. Click **Create app**
3. For API-only access, create directly in Dev Dashboard (no CLI needed)

### Step 2: Get Client Credentials

1. Go to your app → **Settings**
2. Copy **Client ID** and **Client Secret**
3. Store in `.env`:

```env
SHOPIFY_SHOP_NAME=your-store.myshopify.com
SHOPIFY_CLIENT_ID=your_client_id
SHOPIFY_CLIENT_SECRET=your_client_secret
```

### Step 3: Configure Access Scopes

In Dev Dashboard → Your App → **Configuration**, add required scopes.

**Common scopes for product management:**
```
read_products, write_products, read_inventory, write_inventory
```

**Full access scopes:**
```
read_products, write_products, read_orders, write_orders, read_customers, write_customers, read_inventory, write_inventory, read_fulfillments, write_fulfillments, read_draft_orders, write_draft_orders, read_locations, read_price_rules, write_price_rules, read_discounts, write_discounts, read_content, write_content, read_themes, write_themes, read_files, write_files, read_metaobjects, write_metaobjects, read_metaobject_definitions, write_metaobject_definitions
```

**For URL redirects (SEO):**
```
read_online_store_navigation, write_online_store_navigation
```

### Step 4: Install App on Store

1. In Dev Dashboard → Your App → **Test your app** or **Install**
2. Select your store and install

### Step 5: Generate Access Token

Use Client Credentials Grant to get a token:

```bash
curl -X POST \
  "https://{shop}.myshopify.com/admin/oauth/access_token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id={client_id}" \
  -d "client_secret={client_secret}"
```

**Response:**
```json
{
  "access_token": "shpat_xxxxxxxxxxxxx",
  "scope": "write_products,read_orders,...",
  "expires_in": 86399
}
```

Add to `.env`:
```env
SHOPIFY_ACCESS_TOKEN=shpat_xxxxxxxxxxxxx
```

### Step 6: Test the Token

```bash
curl -s -X POST \
  "https://{shop}.myshopify.com/admin/api/2025-01/graphql.json" \
  -H "Content-Type: application/json" \
  -H "X-Shopify-Access-Token: {access_token}" \
  -d '{"query": "{ shop { name } }"}'
```

## Important Notes

- **Token expires in 24 hours** - refresh by repeating Step 5
- **Empty scopes** in response means no scopes configured - update in Dev Dashboard
- **"app_not_installed" error** - install the app on your store first (Step 4)
- **"ACCESS_DENIED" on queries** - add required scopes and generate new token
- After updating scopes, **generate a new token** (old tokens don't get new permissions)

## Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `app_not_installed` | App not installed on store | Install via Dev Dashboard |
| `ACCESS_DENIED` | Missing required scope | Add scope in Dev Dashboard, get new token |
| `Invalid API key` | Wrong credentials or token | Verify client_id/secret, regenerate token |
| Empty `scope` in response | No scopes configured | Configure scopes in Dev Dashboard |

## Token Prefixes

| Prefix | Type | Use |
|--------|------|-----|
| `shpat_` | Admin API access token | Admin API requests |
| `shpss_` | Storefront API token | Storefront API only (won't work for Admin) |

## Additional Resources

- [Access Scopes Reference](https://shopify.dev/docs/api/usage/access-scopes)
- [Client Credentials Grant](https://shopify.dev/docs/apps/build/authentication-authorization/access-tokens/client-credentials-grant)
- [Dev Dashboard](https://shopify.dev/docs/apps/build/dev-dashboard)
