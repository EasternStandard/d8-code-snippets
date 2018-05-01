---
description: How to handle twig includes with sub theme overrides.
---

# Sub Theme Custom Twig Extension Include Function

### Setting Preprocess Variables

In the main theme file, define the parent theme name and the parent theme path explicitly. Then programmatically get the current theme name and compare it to the defined parent theme name. If they are different, set the sub theme variables.

```php
function penn_global_preprocess(array &$vars, $hook) {

  // Set the parent theme variables.
  $vars['parent_theme'] = 'penn_global';
  $vars['parent_theme_path'] = 'themes/custom/' . $vars['parent_theme'];
  
  // Get the theme name.
  $sub_theme_name = \Drupal::service('theme.manager')->getActiveTheme()->getName();
  
  // If the theme name is not the same as the defined parent theme name.
  if($sub_theme_name != $vars['parent_theme']) {
    // Set the sub theme variables.
    $vars['sub_theme'] = $sub_theme_name;
    $vars['sub_theme_path'] = 'themes/custom/' . $vars['sub_theme'];
  }

}
```

### Module File Structure

Now create the module that will contain the Twig Extension. The folder structure will end up looking similar to this:

* penn\_global\_twig\_theme\_include
  * penn\_global\_twig\_theme\_include.info.yml
  * penn\_global\_twig\_theme\_include.services.yml
  * src
    * Theme\_Include\_Twig\_Extension.php

### Info File _\(penn\_global\_twig\_theme\_include.info.yml\)_

The info.yml file can be simple, nothing crazy required here.

```yaml
name: Penn Global Twig Theme Include
description: Adds a custom Twig include function for working with sub-themes
package: Penn Global
type: module
core: 8.x
```

### Services File _\(penn\_global\_twig\_theme\_include.services.yml\)_

This is part of what is required to add an extension to Twig. That's about all I know, maybe someone more knowledgeable on services can add some info here. 

I included the syntax in comments above confusing lines.

```yaml
services:
  # [module-folder-name].[module-class-name]
  penn_global_twig_theme_include.Theme_Include_Twig_Extension:
    # Drupal\[module-folder-name].[module-class-name]
    class: Drupal\penn_global_twig_theme_include\Theme_Include_Twig_Extension
    tags:
      - { name: twig.extension }
```

### The Meat of the Module _\(Theme\_Include\_Twig\_Extension.php\)_

Define a class that extends `Twig_Extension` and contains the function logic.

```php
<?php

namespace Drupal\penn_global_twig_theme_include;

class Theme_Include_Twig_Extension extends \Twig_Extension {

	public function getName() {
		return 'penn_global_twig_theme_include.theme_include_twig_extension';
	}

	public function getFunctions() {
		return array(
			// Create a custom twig function with all the options the default twig include requires.
			new \Twig_SimpleFunction('theme_include', [$this, 'theme_include'], array('needs_environment' => true, 'needs_context' => true, 'is_safe' => array('all')))
		);
	}

	public function theme_include(\Twig_Environment $env, $context, $template, $variables = array(), $withContext = true, $ignoreMissing = false, $sandboxed = false) {

		// If a sub theme exists.
		if($context['sub_theme']) {
			// Get the sub theme path.
			$sub_theme_file_path = $context['sub_theme_path'] . $template;
			// If the file exists.
			if(file_exists($sub_theme_file_path)) {
				// Call the default twig include function.
				return twig_include($env, $context, $sub_theme_file_path, $variables, $withContext, $ignoreMissing, $sandboxed);
			}
		}

		// If a parent theme exists.
		if($context['parent_theme']) {
			// Get the parent theme path.
			$parent_theme_file_path = $context['parent_theme_path'] . $template;
			// If the file exists.
			if(file_exists($parent_theme_file_path)) {
				// Call the default twig include function.
				return twig_include($env, $context, $parent_theme_file_path, $variables, $withContext, $ignoreMissing, $sandboxed);
			}
		}

		// No theme has the file.
		echo 'Missing File: ' . $template;

	}

}
```

### The Result

Now in our Twig templates we can use the Twig function `theme_include()` like so:

```php
<header>
    {{ theme_include('/templates/header/header.html.twig') }}
</header>
```

{% hint style="info" %}
The path should start from within the theme directory.
{% endhint %}



