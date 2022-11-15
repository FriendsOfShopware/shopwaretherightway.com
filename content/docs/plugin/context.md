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

## Context

The normal `Context` can be created using `Context::createDefaultContext`

{{< hint info >}}
By using createDefaultContext the default language and default currency will be used
{{< /hint >}}


### SalesChanelContext

A `SalesChannelContext` can be created using service `Shopware\Core\System\SalesChannel\Context\SalesChannelContextFactory`. 
Inject it into your class and call the `create` function with an random generated token (normally the user session id) and the salesChannelId.

With the third `$options` you can override the logged in user, the used language, the currency and more.




