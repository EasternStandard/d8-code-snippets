---
description: How to handle twig includes with sub theme overrides.
---

# Sub Theme Custom Twig Extension Include Function

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

Now create the module that will contain the Twig Extension

