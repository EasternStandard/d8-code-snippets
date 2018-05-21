---
description: 'Example: prepare a media bundle image of a node in a page preprocess function'
---

# Prepare a view-mode specific renderable variable in a preprocess function

$node = $vars\['node'\];  
$vars\['field\_page\_image\_view'\] = $node-&gt;field\_page\_image-&gt;view\('full'\);



