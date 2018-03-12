# Load current path entity based on current URL

This snippet returns returns a loaded entity. This can be placed in any Entity preprocess.

https://api.drupal.org/api/drupal/core%21core.services.yml/service/path.current/8.2.x



```php
use Drupal\node\Entity\ENTITY;

function preprocess_function() {
  // Loads current path.
  $current_path = \Drupal::service('path.current')->getPath();

  // If path contains the entity I am seeking: Node, Taxonomy, etc.

  if (strpos($current_path, 'ENTITY')) {
    $delimiter = "/";
    $delimited_path = explode($delimiter, $current_path);

    //Entity ID
    $id = end($delimited_path);

    // Use appropriate Entity load: Example, 
    Entity::load($id);
  }
}
```



