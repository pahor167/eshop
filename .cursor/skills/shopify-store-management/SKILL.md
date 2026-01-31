---
name: shopify-store-management
description: Manage Shopify store products, translations, markets, shipping, and media via GraphQL Admin API. Use for creating/updating products with variants, multi-language support, market configuration, inventory management, and image uploads.
---

# Shopify Store Management Skill

Complete guide for managing a Shopify store programmatically via the Admin GraphQL API.

## Prerequisites

- Valid Shopify access token with appropriate scopes
- Store URL (e.g., `yourstore.myshopify.com`)

### Required Scopes

For full store management, ensure these scopes are enabled:

```
read_products, write_products, read_orders, write_orders, read_customers, write_customers,
read_inventory, write_inventory, read_fulfillments, write_fulfillments, read_locations, 
write_locations, read_publications, write_publications, read_locales, write_locales, 
read_translations, write_translations, read_markets, write_markets, read_content, 
write_content, read_shipping, write_shipping
```

## API Endpoint

```
POST https://{store}.myshopify.com/admin/api/2025-01/graphql.json
Headers:
  Content-Type: application/json
  X-Shopify-Access-Token: {access_token}
```

---

## 1. Product Management

### Create Product with Variants

Use `productSet` mutation for creating products with options and variants:

```graphql
mutation productSet($input: ProductSetInput!) {
  productSet(input: $input) {
    product {
      id
      title
      variants(first: 20) {
        edges { node { id title price } }
      }
    }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "input": {
    "title": "Product Name",
    "descriptionHtml": "<h2>Description</h2><p>Details here</p>",
    "vendor": "Your Brand",
    "productType": "Category",
    "tags": ["tag1", "tag2"],
    "status": "ACTIVE",
    "seo": {
      "title": "SEO Title | Brand",
      "description": "Meta description for search engines"
    },
    "productOptions": [
      {
        "name": "Size",
        "values": [{"name": "Small"}, {"name": "Medium"}, {"name": "Large"}]
      }
    ],
    "variants": [
      {"optionValues": [{"optionName": "Size", "name": "Small"}], "price": 29.00},
      {"optionValues": [{"optionName": "Size", "name": "Medium"}], "price": 39.00},
      {"optionValues": [{"optionName": "Size", "name": "Large"}], "price": 49.00}
    ]
  }
}
```

### Update Variant Prices

```graphql
mutation productVariantsBulkUpdate($productId: ID!, $variants: [ProductVariantsBulkInput!]!) {
  productVariantsBulkUpdate(productId: $productId, variants: $variants) {
    productVariants { id title price }
    userErrors { field message }
  }
}
```

### Delete Product

```graphql
mutation { productDelete(input: {id: "gid://shopify/Product/123"}) { deletedProductId } }
```

---

## 2. Inventory Management

### Set Inventory Quantities

```graphql
mutation inventorySetQuantities($input: InventorySetQuantitiesInput!) {
  inventorySetQuantities(input: $input) {
    inventoryAdjustmentGroup { changes { name delta } }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "input": {
    "name": "available",
    "reason": "correction",
    "ignoreCompareQuantity": true,
    "quantities": [
      {"inventoryItemId": "gid://shopify/InventoryItem/123", "locationId": "gid://shopify/Location/456", "quantity": 10}
    ]
  }
}
```

### Get Inventory Item IDs

```graphql
{
  products(first: 10) {
    edges {
      node {
        title
        variants(first: 20) {
          edges {
            node {
              id
              title
              inventoryItem { id requiresShipping }
            }
          }
        }
      }
    }
  }
  locations(first: 1) { edges { node { id name } } }
}
```

---

## 3. Publishing Products

### Get Publication ID (Online Store)

```graphql
{ publications(first: 10) { edges { node { id name } } } }
```

### Publish Product

```graphql
mutation publishProduct($id: ID!, $input: [PublicationInput!]!) {
  publishablePublish(id: $id, input: $input) {
    publishable { availablePublicationsCount { count } }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "id": "gid://shopify/Product/123",
  "input": [{"publicationId": "gid://shopify/Publication/456"}]
}
```

---

## 4. Multi-Language Support

### Enable Locales

```graphql
mutation shopLocaleEnable($locale: String!) {
  shopLocaleEnable(locale: $locale) {
    shopLocale { locale name published }
    userErrors { field message }
  }
}
```

Common locales: `cs` (Czech), `de` (German), `en` (English), `fr` (French), `es` (Spanish)

### Publish Locale

```graphql
mutation shopLocaleUpdate($locale: String!, $shopLocale: ShopLocaleInput!) {
  shopLocaleUpdate(locale: $locale, shopLocale: $shopLocale) {
    shopLocale { locale published }
  }
}
```

Variables: `{"locale": "cs", "shopLocale": {"published": true}}`

### Get Translatable Content

```graphql
{ 
  translatableResource(resourceId: "gid://shopify/Product/123") {
    translatableContent { key value digest locale }
  }
}
```

### Add Translations

```graphql
mutation translationsRegister($resourceId: ID!, $translations: [TranslationInput!]!) {
  translationsRegister(resourceId: $resourceId, translations: $translations) {
    translations { key value locale }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "resourceId": "gid://shopify/Product/123",
  "translations": [
    {"key": "title", "value": "Přeložený název", "locale": "cs", "translatableContentDigest": "abc123..."}
  ]
}
```

> [!IMPORTANT]
> The `translatableContentDigest` must match the original content's digest from `translatableResource` query.

### Translatable Resource Types

- `ONLINE_STORE_THEME` - Theme content (headings, buttons, sections)
- `LINK` - Navigation menu items
- `PRODUCT` - Product titles, descriptions, SEO
- `PAGE` - Static pages
- `COLLECTION` - Collection titles and descriptions

---

## 5. Markets Configuration

### Create Market

```graphql
mutation marketCreate($input: MarketCreateInput!) {
  marketCreate(input: $input) {
    market { id name handle enabled }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "input": {
    "name": "European Union",
    "handle": "eu",
    "enabled": true,
    "regions": [
      {"countryCode": "DE"},
      {"countryCode": "AT"},
      {"countryCode": "FR"}
    ]
  }
}
```

### Create Web Presence (Link Market to Locale)

```graphql
mutation marketWebPresenceCreate($marketId: ID!, $webPresence: MarketWebPresenceCreateInput!) {
  marketWebPresenceCreate(marketId: $marketId, webPresence: $webPresence) {
    market {
      id name
      webPresence { defaultLocale { locale } rootUrls { locale url } }
    }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "marketId": "gid://shopify/Market/123",
  "webPresence": {
    "defaultLocale": "de",
    "subfolderSuffix": "eu"
  }
}
```

### Add Alternate Locales to Market

```graphql
mutation marketWebPresenceUpdate($webPresenceId: ID!, $webPresence: MarketWebPresenceUpdateInput!) {
  marketWebPresenceUpdate(webPresenceId: $webPresenceId, webPresence: $webPresence) {
    market { webPresence { alternateLocales { locale } } }
  }
}
```

Variables: `{"webPresenceId": "gid://shopify/MarketWebPresence/123", "webPresence": {"alternateLocales": ["en"]}}`

---

## 6. Image Upload

### Step 1: Create Staged Upload

```graphql
mutation stagedUploadsCreate($input: [StagedUploadInput!]!) {
  stagedUploadsCreate(input: $input) {
    stagedTargets {
      url
      resourceUrl
      parameters { name value }
    }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "input": [{
    "filename": "product-image.png",
    "mimeType": "image/png",
    "resource": "PRODUCT_IMAGE",
    "fileSize": "500000"
  }]
}
```

### Step 2: Upload File to Staged URL

```bash
curl -X PUT "{staged_url}" \
  -H "Content-Type: image/png" \
  --data-binary "@/path/to/image.png"
```

> [!WARNING]
> Do NOT include `x-goog-acl` header - it will cause authentication errors.

### Step 3: Attach to Product

```graphql
mutation productCreateMedia($productId: ID!, $media: [CreateMediaInput!]!) {
  productCreateMedia(productId: $productId, media: $media) {
    media { ... on MediaImage { id status } }
    mediaUserErrors { field message }
  }
}
```

Variables:
```json
{
  "productId": "gid://shopify/Product/123",
  "media": [{
    "originalSource": "https://shopify-staged-uploads.storage.googleapis.com/tmp/.../image.png",
    "mediaContentType": "IMAGE",
    "alt": "Product image description"
  }]
}
```

---

## 7. Shipping Configuration

### Get Delivery Profiles

```graphql
{
  deliveryProfile(id: "gid://shopify/DeliveryProfile/123") {
    profileLocationGroups {
      locationGroupZones(first: 20) {
        edges {
          node {
            zone { name }
            methodDefinitions(first: 10) {
              edges {
                node {
                  id name active
                  methodConditions {
                    conditionCriteria { ... on MoneyV2 { amount currencyCode } }
                    field operator
                  }
                  rateProvider { ... on DeliveryRateDefinition { price { amount } } }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

### Delete Shipping Method

```graphql
mutation deliveryProfileUpdate($id: ID!, $profile: DeliveryProfileInput!) {
  deliveryProfileUpdate(id: $id, profile: $profile) {
    profile { id }
    userErrors { field message }
  }
}
```

Variables:
```json
{
  "id": "gid://shopify/DeliveryProfile/123",
  "profile": {
    "methodDefinitionsToDelete": ["gid://shopify/DeliveryMethodDefinition/456"]
  }
}
```

---

## Common Patterns

### Batch Operations

When updating multiple items, use bulk mutations where available:
- `productVariantsBulkUpdate` for multiple variants
- `inventorySetQuantities` with multiple quantities array
- `translationsRegister` with multiple translations array

### Error Handling

Always check `userErrors` in mutation responses:
```json
{
  "userErrors": [
    {"field": ["input", "price"], "message": "Price must be positive"}
  ]
}
```

### Pagination

Use cursor-based pagination for large datasets:
```graphql
{
  products(first: 50, after: "cursor123") {
    edges { node { id } cursor }
    pageInfo { hasNextPage endCursor }
  }
}
```
