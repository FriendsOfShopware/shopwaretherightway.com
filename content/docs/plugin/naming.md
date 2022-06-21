---
weight: 10
title: Nameing
hidden: true
---

# Naming

There are 2 hard problems in computer science: cache invalidation, naming things. Correct naming is in an ecosystem not enough, as maybe another smart people named it exactly like you. (But honestly we both know nobody uses the fancy variable `active` other than us!) 

Let's look directly into an example by names like `active` are bad in an unknown system. We have a simple code which adds a variable to the Twig template.

```php
<?php

// catching an event

$event->getTwig()->addGlobal('active', $this->isMyPluginActive());

```

This works for us, we have the new variable `active` in Twig. It has our correct expected value, and now we install another extension from someone else, and we see our extension is not working. The smart developer also uses the same variable.
For such scenarios, you should always prepend to variable names your plugin prefix like `froshToolsActive`, so we won't collide anymore.

This does affect many things like:

- Twig Variables
- SCSS Variables
- Database Tables/Columns
- DAL Fields/Associations
- Theme Config Variables
- Admin Mixin/Component/Module
- Storefront JS Plugin


{{< hint info >}}
It is also highly recommended to use English variable names, so every developer can understand it
{{< /hint >}}