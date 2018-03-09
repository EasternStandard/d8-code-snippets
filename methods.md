# Node: Get top most menu link parent

This function returns the top most menu link parent of the node.

MenuLinkManager documentation:

[https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Menu%21MenuLinkManager.php/class/MenuLinkManager/8.2.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Menu%21MenuLinkManager.php/class/MenuLinkManager/8.2.x)

```php
function get_parent_menu_title($node_id) {
  // Load MenuLinkManager Drupal Service
  $menu_link_manager = \Drupal::service('plugin.manager.menu.link');

  if ($node_id) {
    // Load node menu link plugin instance
    $menu_link = $menu_link_manager->loadLinksByRoute('entity.node.canonical', array('node' => $node_id));
  }
  else {
    return '';
  }

  if (is_array($menu_link) && count($menu_link)) {
    $menu_link = reset($menu_link);

    if ($menu_link->getParent()) {
      $parents = $menu_link_manager->getParentIds($menu_link->getParent());
      $parent = end($parents);
      $title = $menu_link_manager->createInstance($parent)->getTitle();

      return $title;
    }
  }

  return '';
}
```



