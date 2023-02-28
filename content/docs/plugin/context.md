---
weight: 10
title: Context
hidden: true
---

# Context

Plugin services should always get passed the `Context` or `SalesChannelContext` object to be able to better unit test the service. 
Only the entrypoint like the Shopware Request or an Command should create the `Context`.

## Getting the current Context

For each Request the normal `Context` will be created and lives in the attributes of the Request object. When it is a Storefront Request the attributes also contains the SalesChannelContext.

```php
/** @var Context $context */
$context = $request->attributes->get(PlatformRequest::ATTRIBUTE_CONTEXT_OBJECT);
```

```php
/** @var SalesChannelContext $context */
$salesChannelContext = $request->attributes->get(PlatformRequest::ATTRIBUTE_SALES_CHANNEL_CONTEXT_OBJECT);
```

## Creating a Context

### Context

The normal `Context` can be created using `Context::createDefaultContext`

{{< hint info >}}
By using createDefaultContext the default language and default currency will be used.
{{< /hint >}}

The given language inside the Context will be used for all default translations. When the default language differs from the context language, the default translation has to be given in the translations array. Example

```json
{
    "name": "Context Language",
    "translations": {
        "LANGUAGE-ID": {
            "name": "Language Translation"
        }
    }
}
```


### SalesChannelContext

A `SalesChannelContext` can be created using service `Shopware\Core\System\SalesChannel\Context\SalesChannelContextFactory`. 
Inject it into your class and call the `create` function with an random generated token (normally the user session id) and the salesChannelId.

With the third `$options` you can override the logged in user, the used language, the currency and more.

### API

In the API the Context can be controlled using the `sw-language-id` and `sw-currency-id` header.

```http
GET /api/search/product
sw-language-id: my-language-id
```

All content will be in the translated property in that language.

```json
{
    "data": [
        {
            "id": "my-product-id",
            "translated": {
                "name": "My Product"
            }
        }
    ]
}
```