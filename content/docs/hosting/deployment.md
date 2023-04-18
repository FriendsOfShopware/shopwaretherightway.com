---
weight: 10
title: Deployment
hidden: true
---

# Deployment

Each Deployment looks different but some things are equals between multiple ways to deploy. This site list some best practises todo.

## Plugins

All Plugins should be managed by Composer. If you have project specific plugins you should put them into `custom/static-plugins` and require the plugin then or build them into the `src` directory without building a Plugin. You can refer also to [this Blog Post](https://shyim.me/blog/you-dont-need-a-plugin-to-customize-shopware-6/) if you want to customize Shopware without Plugins.

## DB-less building

When all plugins are installed with Composer, you can use `bin/ci` instead of `bin/console`. 
This command does not use the Database for basic commands like `bundle:dump` which are required to build the Storefront / Administration assets. So in this way you can build all assets of your project once. 

## Building assets

When you are building the Assets make sure to set the following environment variables:

```
NODE_ENV=production
# Disable Puppeteer Downloading
PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
# Disable Typescript Checking
DISABLE_ADMIN_COMPILATION_TYPECHECK=true
# Build only the extensions not the Administration
SHOPWARE_ADMIN_BUILD_ONLY_EXTENSIONS=1
```

## Composer

There also some flags that are recommanded when you are using Composer:

* `--no-dev` - Don't install dev dependencies (Profiler, use APP_DEBUG=1 in prod / stage debugging)
* `--classmap-authoritative` - Generate only the class map. Disables on-the-fly finding classes.

{{< hint info >}}
Enabling classmap-authoritative with normal plugins installed (custom/plugins) will introduce a lot of performance issues and with Shopware 6.5 this will be disabled on the fly. Only use this option when all plugins are installed with Composer.
{{< /hint >}}

Also make sure that you set the correct PHP version inside the `composer.json` to fetch the dependencies for the right PHP version (without using the excact same version inside your CI).

```json
{
    "config": {
        "platform": {
            "php": "8.1.8"
        }
    },
    "require": {
        ...
    }
}
```

## Splitting Tasks

The most intensive tasks like building assets, installing composer packages should be done in the CI. And this artifact/image should be transfered to the production server. 

From there you can update all plugins by `bin/console plugin:refresh` and `bin/console plugin:update ""` on the server. And maybe build the theme `bin/console theme:compile`.

