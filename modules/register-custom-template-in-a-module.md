# Register Custom Template in a Module

Assuming a folder structure of:

```text
modules
    -> custom
        -> MY_MODULE
            -> templates
                -> [CONTENT_TYPE_1]
                    -> [TEMPLATE-1-USING-THEME-SUGGESTION.html.twig]
                -> [CONTENT_TYPE_2]
                    -> [TEMPLATE-2-USING-THEME-SUGGESTION.html.twig]
```

The following implementation of hook\_theme will register your module's templates:

{% code-tabs %}
{% code-tabs-item title="MY\_MODULE.module" %}
```php
<?php

/**
 * Implements hook_theme().
 */
function MY_MODULE_theme($existing, $type, $theme, $path) {
  $templatePath = "{$path}/templates";
  
  return [
    'template_1_using_theme_suggestion' => [
      'template' => 'template-1-using-theme-suggestion',
      'path' => "{$templatePath}/content_type_1",
      'base hook' => 'node, paragraph, field, etc.',
    ],
    'template_2_using_theme_suggestion' => [
      'template' => 'template-2-using-theme-suggestion',
      'path' => "{$templatePath}/content_type_2",
      'base hook' => 'node, paragraph, field, etc.',
    ],
  ];
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Remember to run `drush cr` after changing this method to ensure your latest changes are picked up.

