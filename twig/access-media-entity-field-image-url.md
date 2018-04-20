---
description: >-
  Accessing a media entity field's image URI value can be tricky when trying to
  access it with a preprocess function. The snippet below shows how you can
  access the image's URI in the twig template.
---

# Access Media Entity field image URL

Notes: This uses the `node` object that is available in the `node.html.twig` template. Other examples to come for different entities.

```markup
node.FIELD_MEDIA_ENTITY.entity.FIELD_IMAGE_FIELD_IN_MEDIA_ENTITY.entity.getFileUri
```

This can be couple with the `twig_tweak` module's `image_style` filter to generate a image\_style URL_._

```markup
{{ node.FIELD_MEDIA_ENTITY.entity.FIELD_IMAGE_FIELD_IN_MEDIA_ENTITY.entity.getFileUri|image_style('IMAGE_STYLE') }}
```

* Recommended modules:
  * Twig Tweak [https://www.drupal.org/project/twig\_tweak](https://www.drupal.org/project/twig_tweak)

