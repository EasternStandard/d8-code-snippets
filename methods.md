# Node: Get top most menu link parent
This function returns the top most menu link parent of the node.


```
function get_parent_menu_title($node_id) {
  $menu_link_manager = \Drupal::service('plugin.manager.menu.link');

  if ($node_id) {
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


