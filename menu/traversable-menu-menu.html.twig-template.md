# Traversable Menu menu.html.twig Template

## Download latest version of traversable Menu

If there are any uses relating to the traversable menu, please open an issue in the Github project:

[https://github.com/jimkeller/traversable\_menu](https://github.com/jimkeller/traversable_menu)

## Modify menu.html.twig

Apply this template to a theme suggested menu.html.twig.

```text
{#
/**
 * @file
 * Theme override to display a menu.
 *
 * Available variables:
 * - menu_name: The machine name of the menu.
 * - items: A nested list of menu items. Each menu item contains:
 *   - attributes: HTML attributes for the menu item.
 *   - below: The menu item child items.
 *   - title: The menu link title.
 *   - url: The menu link url, instance of \Drupal\Core\Url
 *   - localized_options: Menu link localized options.
 *   - is_expanded: TRUE if the link has visible children within the current
 *     menu tree.
 *   - is_collapsed: TRUE if the link has children within the current menu tree
 *     that are not currently visible.
 *   - in_active_trail: TRUE if the link is in the active trail.
 */
#}
{% import _self as menus %}

{#
  We call a macro which calls itself to render the full tree.
  @see http://twig.sensiolabs.org/doc/tags/macro.html
#}
{{ menus.menu_links(items, attributes, 0) }}

{% macro menu_links(items, attributes, menu_level) %}
  {% import _self as menus %}
  {% if items %}
    <div class="menu__panel">

    {% for item in items %}

      {% if (loop.first) %}

        {% if menu_level == 0 %}
          <ul class="main-menu--primary menu-container__level{{ menu_level }}" {{ attributes }}>
        {% else %}
          <ul>
            <li class="menu-item__level{{ menu_level }} menu-item__back" {{ item.attributes }}>
              {% if (0) %}
              <div class="back-to-top">
                <a href="#" class="menu__panel__trigger--top">Up to main menu</a>
              </div>
              {% endif %}
              <div class="back">
                <a href="#" class="menu__panel__trigger--parent">
                  Back to previous menu
                </a>
              </div>
            </li>
        {% endif %}

        <li class="menu__panel__title__item"><div class="menu__panel__title"></div></li>      

      {% endif %}

      {%
      set classes = [
        'menu-item__link',
        "menu-item__level#{ menu_level }",
        item.is_expanded ? 'menu-item--expanded',
        item.is_collapsed ? 'menu-item--collapsed',
        item.in_active_trail ? 'menu-item--active-trail',
      ]
      %}

      <li {{ item.attributes.addClass(classes) }}>

        <div class="link">
          {{ link(item.title, item.url) }}
          {% if item.below %}
            <a class="menu-item__explore menu__panel__trigger--child" href="#">Explore</a>
          {% endif %}
        </div>
        {% if item.below %}
          {{ menus.menu_links(item.below, attributes, menu_level + 1) }}
        {% endif %}

      </li>
    {% endfor %}
    </ul>
  </div>
  {% endif %}
{% endmacro %}
```

## Initialize traversable menu in your JS file; modify any configurations if using custom classes

```javascript
'use strict';

import TraversableMenu from './vendor/traversable_menu.js';

// Loose example
Drupal.behaviors.CHANGE_ME = {
  attach: function(context, settings) {
    let traversable = new TraversableMenu({
      selectors: {
        'panels_container': '.drawer-navigation .traversable-menu',
        'menu_item_active': '.menu-item--active-trail',
        'menu_item': 'li.menu-item__link',
        'menu_item_link': '.link a'
      },
      triggers: {
        'parent_text': '<i class="icon--left-chevron-button--penn-gray"></i><span>Back to [:previous-title:] menu</span>',
        'top_text': '<i class="icon--up-to-button--penn-gray"></i><span>Up to Main Menu</span>'
      }
    });
  }
}
```

