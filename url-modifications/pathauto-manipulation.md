# PathAuto Manipulation

This snippet is an example of how to modify pathauto patterns before they are processed and saved.

```php
<?php

use Drupal\taxonomy\Entity\Term;
use Drupal\Core\Render\BubbleableMetadata;

/**
 * Implements hook_pathauto_pattern_alter().  This example assumes a pattern starting with a
 * [node:field_taxonomy_reference:name] or [node:field_taxonomy_reference:entity:root:name]
 * token that we want to remove if it will become 'example-str' in the finished url.
 *
 * @param   array  $pattern  A pathauto context array
 * @param   array  $context  Information about the source of the call (e.g., the raw node path)
 * 
 * @return  void
 */
function MODULE_pathauto_pattern_alter(&$pattern, array $context) : void
{
  if ($context['module'] == 'node' && !empty($context['data']['node'])) {
    $node = $context['data']['node'];
    
    if ($node->hasField('field_taxonomy_reference')) {
      $primary = $node->get('field_taxonomy_reference');
      
      // let form validation handle it if they forgot to set the primary department
      if (!$primary->isEmpty()) {
        $tid = (int) $primary->getString();
        $term = \_MODULE_get_root_term($tid);
        
        $name = \strtolower($term->getName());
        
        // the check is on the taxonomy name, not the sanitized pathauto string, so keep in spaces, ampersands, etc.
        if ($name === 'example str') {
          // if the pattern does not start with the token, the regex pattern here will need to be changed
          $matches = [];
          $check = \preg_match('/(\[node:field_taxonomy_reference:[\w:]+\])(.*)/', $pattern->getPattern(), $matches);

          // preg_match() returns 0 on no match, and false on error; since preg_match() is old it has weird error
          // handling methods that we have to deal with
          if ($check === false) {
            $code = \preg_last_error();
            $message = \_MODULE_preg_errtxt($code);
            
            \Drupal::logger('MODULE')->error('Error matching pattern (' . $code . '): ' . $message);
            return;
          }

          // since we are only messing with the first token at the start of the pattern, we only need the end match
          $new_pattern = \end($matches);
          if (\strpos($new_pattern, '[') === false) {
            $new_pattern = \implode('/', [end($matches), '[node:title]']);
          }
          
          $new_pattern = \str_replace('//', '/', $new_pattern);
          
          if ($check === 0 || \empty($new_pattern)) {
            \Drupal::logger('MODULE')->error('Unable to modify pattern: ' . $pattern->getPattern());
          } else {
            $pattern->setPattern($new_pattern);
          }
        } elseif ($name === 'some specific term') {
          // for a very specific term we want to change the text entirely instead of just stripping out the 
          $matches = [];
          $check = \preg_match('/(\[node:field_pg_all_rtt_department:[\w:]+\])\/?(.*)/', $pattern->getPattern(), $matches);
          $pattern->setPattern(\implode('/', ['someothertext', end($matches)]));
        }
      }
    }
  }
}

/**
 * Recursively climb the taxonomy tree until we find the root term
 *
 * @param   integer  $tid
 * @return  Term
 */
function _MODULE_get_root_term(int $tid) : Term
{
    $parents = \Drupal::entityManager()->getStorage('taxonomy_term')->loadParents($tid);
    if (\empty($parents)) {
        $term = Term::load($tid);

        return $term;
    } else {
        $term = \reset($parents);

        return \_MODULE_get_root_term((int) $term->id());
    }
}

/**
 * From http://php.net/manual/en/function.preg-last-error.php
 * Since I stole this from the internet, I'm not going to explain it.
 *
 * @param   int          $errcode
 * @return  string|null
 */
function _MODULE_preg_errtxt($errcode)
{
  static $errtext;

  if (!\isset($errtxt)) {
    $errtext = [];
    $constants = \get_defined_constants(true);
    
    foreach ($constants['pcre'] as $c => $n) {
      if (\preg_match('/_ERROR$/', $c)) {
        $errtext[$n] = $c;
      }
    }
  }

  return \array_key_exists($errcode, $errtext) ? $errtext[$errcode] : null;
}

```





