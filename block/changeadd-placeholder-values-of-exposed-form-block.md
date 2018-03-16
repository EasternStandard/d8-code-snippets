# Change/add placeholder values of Exposed Form Block

Useful for search forms that require different placeholder texts depending on its block placement.

```php
function THEME_preprocess_block(&$variables) {
  // get block ID
  $block_id = $variables['elements']['#id'];

  // checks for specific block to make changes to
  if ($block_id == 'BLOCKID') {
    // changes text of submit button
    $variables['content']['actions']['submit']['#value'] = 'Search';

    // changes placeholder text of text field
    // search_api_fulltext is the field name of the text field. Replace with appropriate field name.
    $variables['content']['search_api_fulltext']['#placeholder']= 'Enter Keywords';
  }
}
```



