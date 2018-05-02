---
description: How to handle twig includes with sub theme overrides.
---

# Sub Theme Custom Twig Extension Include Function

### Module File Structure

Create the module that will contain the Twig Extension. The folder structure should  this:

* twig\_extension\_theme\_include
  * twig\_extension\_theme\_include.info.yml
  * twig\_extension\_theme\_include.services.yml
  * src
    * Theme\_Include\_Twig\_Extension.php

### Info File _\(twig\_extension\_theme\_include.info.yml\)_

The info.yml file can be simple, nothing crazy required here.

```yaml
name: Twig Extension Theme Include
description: Extends Twig core with a custom include function for proper file overrides when working with sub themes.
package: Custom
type: module
core: 8.x
```

### Services File _\(twig\_extension\_theme\_include.services.yml\)_

This is part of what is required to add an extension to Twig. That's about all I know, maybe someone more knowledgeable on services can add some info here. 

I included the syntax in comments above confusing lines.

```yaml
services:
  # [module-folder-name].[module-class-name]
  twig_extension_theme_include.Theme_Include_Twig_Extension:
    # Drupal\[module-folder-name].[module-class-name]
    class: Drupal\twig_extension_theme_include\Theme_Include_Twig_Extension
    tags:
      - { name: twig.extension }
```

### The Meat of the Module _\(Theme\_Include\_Twig\_Extension.php\)_

Define a class that extends `Twig_Extension` and contains the function logic.

```php
<?php

namespace Drupal\twig_extension_theme_include;

class Theme_Include_Twig_Extension extends \Twig_Extension {

	public function getName() {
		return 'twig_extension_theme_include.theme_include_twig_extension';
	}

	public function getFunctions() {
		return array(
			// Create a custom twig function with all the options the default twig include requires.
			new \Twig_SimpleFunction('theme_include', [$this, 'theme_include'], array('needs_environment' => true, 'needs_context' => true, 'is_safe' => array('all')))
		);
	}

	public function theme_include(\Twig_Environment $env, $context, $template, $variables = array(), $withContext = true, $ignoreMissing = false, $sandboxed = false) {

		// Initiate an array of the active and parent themes.
		$active_themes = array();

		// Get the active theme.
		$active_theme = \Drupal::service('theme.manager')->getActiveTheme();
		// Append the active theme to the array of all active themes (including parents).
		array_push($active_themes, $active_theme);

		// Set a variable to hold the current theme as we loop through. Set initially to the active theme.
		$loop_theme = $active_theme;
		// While the current loop theme has a base theme.
		while($loop_theme->getBaseThemes()) {
			// Set the loop theme to the base theme.
			$loop_theme = array_shift(array_values($loop_theme->getBaseThemes()));
			// Add the base theme to the array of active themes.
			array_push($active_themes, $loop_theme);
		}

		// For each item in array.
		for($i = 0; $i < count($active_themes); $i++) {
			// Set the theme to a variable.
			$current_theme = $active_themes[$i];
			// Get the path to the current theme.
			$theme_path = $current_theme->getPath();
			// Build the path to the file in the current theme.
			$theme_file_path = $theme_path . '/' . $template;
			// If the file exists in the current theme.
			if(file_exists($theme_file_path)) {
				// Call the default twig include function.
				return twig_include($env, $context, $theme_file_path, $variables, $withContext, $ignoreMissing, $sandboxed);
			}
		}

		// Generate a path to the file in the active theme.
		$active_theme_path = $active_theme->getPath() . $template;
		// No active theme has the file, but we pass the path into the twig include as if the file existed in the active theme directory (let the core twig function handle it).
		return twig_include($env, $context, $active_theme_path, $variables, $withContext, $ignoreMissing, $sandboxed);

	}

}
```

### The Result

Install the module and now we can use `theme_include()` in our Twig templates like so:

```php
<header>
    {{ theme_include('templates/header/header.html.twig') }}
</header>
```

{% hint style="info" %}
Paths should be relative to the theme directory.
{% endhint %}



