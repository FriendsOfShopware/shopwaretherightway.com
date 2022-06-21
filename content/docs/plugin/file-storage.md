---
weight: 10
title: File Storage
hidden: true
---

# File Storage

Saving files is a typical task in Plugins. Shopware has here many options to do this.
But first why you should never `fopen` / `file_put_contents` in a random chosen directory!: 

You don't know how the hosting of the Plugin will be. The chosen folder can be read-only, rotating on any deployment, on an application cluster.
Let's discover all options and find the best fitting one for you.


## Static Assets

As Shopware Plugins are Symfony Bundles they inherit the asset functionality of Symfony. You can add your files into `src/Resources/public` folder, and they will be automatically copied to an external available place for you. This happens all plugin lifecycles (install, update, uninstall). 


Usage of Static Assets:

{{< tabs "static-usage" >}}
{{< tab "Twig" >}}

```twig
{{ asset("<relative-path-to-your-file-from-public>", "@PluginName") }}
```

{{< /tab >}}

{{< tab "PHP" >}}

```php
<?php

// inject using:  <argument type="service" id="assets.packages" />
$packages = $container->get('assets.packages')

$package = $packages->getPackage('asset');

$package->getUrl('<relative-path-to-your-file-from-public>');

```

{{< /tab >}}
{{< /tabs >}}

## Public File system

The second option is to use a public file system. This is a [flexible interface](https://flysystem.thephpleague.com/docs/) to interact with an unknown file system, we just use the abstraction and don't care what is behind it.

The public file system is available in two variants. One scoped special for the extension and one normal. 

{{< hint info >}}
The scoped variant is the recommended variant as you have a complete custom scoped file system for your plugin
{{< /hint >}}

{{< tabs "public-fs" >}}
{{< tab "Scoped Public FS" >}}

[See here for complete interface](https://flysystem.thephpleague.com/v1/docs/usage/filesystem-api/)

This File system writes in the same folder as the global one, but always in a subfolder named like your Plugin to not have any collisions.

```php
<?php

// our extension name is FroshTools, camel-case + .filesystem.public suffix

// inject using:  <argument type="service" id="frosh_tools.filesystem.public" />
$fs = $container->get('frosh_tools.filesystem.public')

$fs->write('file.jpg', 'data');
$fs->read('file.jpg'); // returns data
```

{{< /tab >}}

{{< tab "Global Public FS" >}}

[See here for complete interface](https://flysystem.thephpleague.com/v1/docs/usage/filesystem-api/)

```php
<?php

// inject using:  <argument type="service" id="shopware.filesystem.public" />
$fs = $container->get('shopware.filesystem.public')

$fs->write('file.jpg', 'data');
$fs->read('file.jpg'); // returns data
```

{{< /tab >}}
{{< /tabs >}}


Generating links can be done in the same way using the Asset package `public`

{{< tabs "public-fs-link" >}}
{{< tab "Scoped Public FS" >}}

In this example our Plugin name is `FroshTools` due to camel case its here formatted as `plugins/{name}/path`

```twig
{{ asset("plugins/frosh_tools/file.jpg", "public") }}
```

```php
<?php

// inject using:  <argument type="service" id="assets.packages" />
$packages = $container->get('assets.packages')

$package = $packages->getPackage('public');

$package->getUrl('plugins/frosh_tools/file.jpg');
```

{{< /tab >}}

{{< tab "Global Public FS" >}}

```twig
{{ asset("file.jpg", "public") }}
```

```php
<?php

// inject using:  <argument type="service" id="assets.packages" />
$packages = $container->get('assets.packages')

$package = $packages->getPackage('public');

$package->getUrl('file.jpg');
```

{{< /tab >}}
{{< /tabs >}}


## Private File system

The private file system is similar to the public file system, but as the name already said the written files are not accessible using URLs.

Similar to public file system, there is also scoped file system for your extension.

{{< hint info >}}
The scoped variant is the recommended variant as you have a complete custom scoped file system for your plugin
{{< /hint >}}

{{< tabs "private-fs" >}}
{{< tab "Scoped Private FS" >}}

[See here for complete interface](https://flysystem.thephpleague.com/v1/docs/usage/filesystem-api/)

This File system writes in the same folder as the global one, but always in a subfolder named like your Plugin to not have any collisions.

```php
<?php

// our extension name is FroshTools, camel-case + .filesystem.private suffix

// inject using:  <argument type="service" id="frosh_tools.filesystem.private" />
$fs = $container->get('frosh_tools.filesystem.private')

$fs->write('file.jpg', 'data');
$fs->read('file.jpg'); // returns data
```

{{< /tab >}}

{{< tab "Global Private FS" >}}

[See here for complete interface](https://flysystem.thephpleague.com/v1/docs/usage/filesystem-api/)

```php
<?php

// inject using:  <argument type="service" id="shopware.filesystem.private" />
$fs = $container->get('shopware.filesystem.private')

$fs->write('file.jpg', 'data');
$fs->read('file.jpg'); // returns data
```

{{< /tab >}}
{{< /tabs >}}

{{< hint warning >}}
When you use the global file system, make sure you write in a custom directory and is prefixed with your extension name.
{{< /hint >}}