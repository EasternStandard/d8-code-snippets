# Entity: Load Entity based off URL

This snippet returns returns a loaded entity. This can be placed in any Entity preprocess.

```php
use Drupal\node\Entity\ENTITY;

function preprocess_function() {
  // Loads current path.
  $current_path = \Drupal::service('path.current')->getPath();

  // If path contains the entity I am seeking: Node, Taxonomy, etc.

  if (strpos($current_path, 'ENTITY') !== false) {
    $delimiter = "/";
    $delimited_path = explode($delimiter, $current_path);

    //Entity ID
    $id = end($delimited_path);

    // Use appropriate Entity load: Example,  Node::load($id)
    Entity::load($id);
  }
}
```

## Reference links:

[https://api.drupal.org/api/drupal/core!core.services.yml/service/path.current/8.2.x](https://api.drupal.org/api/drupal/core!core.services.yml/service/path.current/8.2.x)

[https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Entity!Entity.php/function/Entity%3A%3Aload/8.2.x](https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Entity!Entity.php/function/Entity%3A%3Aload/8.2.x)

