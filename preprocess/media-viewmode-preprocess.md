---
description: 'Example: prepare a media bundle image of a node in a page preprocess function'
---

# Prepare a view-mode specific renderable variable in a preprocess function

```php
$node = $vars['node'];
$vars['field_page_image_view'] = $node->field_page_image->view('full');
```



