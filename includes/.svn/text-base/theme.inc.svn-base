<?php

/**
 * Generate the HTML output for a muchomenu item
 */
function theme_muchomenu_item($item, $has_children, $prefix = '', $layout = 'vertical', $menu = '') {

  $li_class = ($menu ? 'expanded ' : ($has_children ? 'collapsed ' : 'leaf '));
  $li_class .= $item['class'];
  switch ($layout) {
    case 'vertical':
      $listyle = '';
      break;

    case 'horizontal':
      $listyle = ' style="float:left;"';
      break;
  }
  // Set active menu item by checking for path alias, then drupal path, then plain path.
  $active_path = drupal_lookup_path("alias", $_GET["q"]) ? drupal_lookup_path("alias", $_GET["q"]) : (drupal_lookup_path("source", $_GET["q"]) ? drupal_lookup_path("source", $_GET["q"]) : $_GET["q"]);
  // Check for the menu item, whether it corresponds with the active item
  if ($item['path'] == '<front>') {
    $item['path'] = variable_get('site_frontpage', 'node');
  }
  $item_path = $active_path == $item['path'] ?
          $item['path'] : ($active_path == drupal_lookup_path("alias", $item['path']) ?
                  $active_path : ($active_path == drupal_lookup_path("source", $item['path']) ?
                          $active_path : $item['path']));

  if ($item_path == $active_path) {
    $li_class .= ' active-trail';
    $a_class = "active";
  }


  if (!empty($item['path'])) {
    $output = '<li class="' . $li_class . '"' . $listyle . '><a href="' . url($item['path']) . '" class="' . $a_class . '"><img src="' .
            base_path() . $item['imagepath'] . '" alt="' . check_plain($item['alt']) . '" title="' . check_plain($item['title'])
            . '" name="muchomenu_' . $prefix . '_' . $item['id'] . '"' . $script . ' /></a>' . $menu . '</li>';
  }
  else {
    $output = '<li class="delimiter"' . $listyle . '><img src="' . base_path() . $item['imagepath'] . '" />' . $menu . '</li>';
  }
  return $output;
}

/**
 * Generate the HTML output for a muchomenu tree
 */
function theme_muchomenu_tree($tree) {
  $output = '<ul class="muchomenu">' . $tree . '</ul>';
  return $output;
}

/**
 * Theme a menu tree
 *
 * This function takes a menu tree, such as primary links, and generates
 * HTML markup of the menu so that it can be styled as a mega menu. It
 * takes the first three nested levels of the menu tree and creates a
 * structure of nested lists with appropriate classes and IDs assigned (even,
 * odd, active, etc.).
 *
 * First, we iterate through the first level of menu items (branch/tier-1/muchomenu-bin). Each
 * item will be the muchomenu-parent of the second level of links (twig/tier-2/muchomenu-slot).
 * Next we iterate through the twigs of the menu tree to fill the muchomenu-bins. A bin
 * is an unordered list which contains slots (twig/tier-2 items). To fill the slots we iterate
 * through each twig, where the leaves are the deepest level of the menu tree (tier-3). Each leaf
 * is a list item containing a tier-3 menu link.
 *
 * Abbreviations: t1, t2, & t3 stands for tier-1, tier-2, and tier-3
 * respectively. They represent nested level menu items.
 *
 * @param $menutree
 *    The menu tree to be marked up (i.e. primary_links)
 * @return
 *    HTML markup for a mega menu
 *
 */
function theme_muchomenu_menu_tree($menu, $tree) {
  $menu_name = $menu['title'];

  $settings = array(
      'animationEffect' => $menu['animation_effect'],
      'animationSpeed' => $menu['animation_speed'],
  );
  drupal_add_js(array('muchomenu' => $settings), 'setting');

  $path = drupal_get_path('module', 'muchomenu');
  drupal_add_js("$path/js/muchomenu.js");
  drupal_add_css("$path/css/muchomenu.css");

  if ($style = variable_get('muchomenu_style_path', '')) {
    drupal_add_css($style);
  }
  else {
    drupal_add_css("$path/css/muchomenu-default-style.css");
  }

  $menu_orientation = $menu['orientation'];
  if ($menu_orientation == 'vertical') {
    $vertical_position = 'flyright';
  }
  else {
    $vertical_position = '';
  }

  $output = sprintf('<ul id="muchomenu-%s" class="muchomenu-menu muchomenu-menu-%s">', $menu['id'], $menu_orientation);

  $count = 0;
  foreach ($tree as $id => $item) {
    $panel_name = $item['link']['panel_name'];
    $link_attributes = array(
        'class' => $item['link']['class'],
        'target' => ($item['link']['target']) ? '_blank' : '',
    );
    if ($item['link']['disable_click']) {
      $link_attributes['class'] .= ' disable-click';
    }
    $count_attributes = _muchomenu_count_attributes($count, sizeof($tree));

    $output .= sprintf('<li class="muchomenu-parent%s">', $count_attributes);
    $output .= sprintf('<span class="muchomenu-parent-title">%s</span>', l($item['link']['title'], $item['link']['path'], array('attributes' => $link_attributes)));

    $panel = panels_mini_load($panel_name);

    if ($panel) {
      $output .= sprintf('<div class="muchomenu-bin muchomenu-slots muchomenu-bin-%s %s">', $item['link']['id'], $vertical_position);
      $output .= panels_render_display($panel->display);
      $output .= '</div>';
    }

    $output .= '</li>';
    $count++;
  }

  $output .= '</ul>' . "\n";

  return $output;
}

/**
 * Theme the menu overview form into a table.
 */
function theme_muchomenu_overview_form($form) {
  drupal_add_tabledrag('menu-overview', 'order', 'sibling', 'menu-weight');

  $header = array(
      t('Menu item'),
      array('data' => t('Enabled'), 'class' => 'checkbox'),
      array('data' => t('New Window'), 'class' => 'checkbox'),
      array('data' => t('Disable Click'), 'class' => 'checkbox'),
      t('Weight'),
      array('data' => t('Operations'), 'colspan' => '3'),
  );

  $rows = array();
  foreach (element_children($form) as $id) {
    if (isset($form[$id]['enabled'])) {
      $element = &$form[$id];
      // Build a list of operations.
      $operations = array();
      foreach (element_children($element['operations']) as $op) {
        $operations[] = drupal_render($element['operations'][$op]);
      }
      while (count($operations) < 2) {
        $operations[] = '';
      }

      // Add special classes to be used for tabledrag.js.
      $element['id']['#attributes']['class'] = 'menu-id';
      $element['weight']['#attributes']['class'] = 'menu-weight';

      // Change the parent field to a hidden. This allows any value but hides the field.
      $element['menu_id']['#type'] = 'hidden';

      $row = array();
      $row[] = theme('indentation', $element['#item']['depth']) . drupal_render($element['title']);
      $row[] = array('data' => drupal_render($element['enabled']), 'class' => 'checkbox');
      $row[] = array('data' => drupal_render($element['target']), 'class' => 'checkbox');
      $row[] = array('data' => drupal_render($element['disable_click']), 'class' => 'checkbox');
      $row[] = drupal_render($element['weight']) . drupal_render($element['menu_id']) . drupal_render($element['id']);
      $row = array_merge($row, $operations);

      $row = array_merge(array('data' => $row), $element['#attributes']);
      $row['class'] = !empty($row['class']) ? $row['class'] . ' draggable' : 'draggable';
      $rows[] = $row;
    }
  }
  $output = '';
  if ($rows) {
    $output .= theme('table', $header, $rows, array('id' => 'menu-overview'));
  }
  $output .= drupal_render($form);
  return $output;
}