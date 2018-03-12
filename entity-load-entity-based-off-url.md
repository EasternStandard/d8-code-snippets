# Load current path entity based on current URL

This snippet returns returns a loaded entity. This can be placed in any Entity preprocess.

```php
// Loads current path.
$current_path = \Drupal::service('path.current')->getPath();

// If path contains the entity I am seeking: Node, Taxonomy, etc.

if (strpos($current_path, 'ENTITY')) {
  $delimiter = "/";
  $delimited_path = explode($delimiter, $current_path);
  
  //Entity ID
  $id = end($delimited_path);
  
  Entity::load($id);
}
```



