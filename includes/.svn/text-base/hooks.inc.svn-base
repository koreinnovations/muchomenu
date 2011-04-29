<?php

/**
 * Implements hook_menu().
 */
function muchomenu_menu() {
  $items = array();
  $admin_access = array('administer muchomenu');


  $items['admin/build/menu/muchomenu'] = array(
      'title' => 'muchomenus',
      'page callback' => 'muchomenu_overview_page',
      'access arguments' => $admin_access,
      'file' => 'includes/admin.inc',
      'weight' => -1,
  );
  $items['admin/build/menu/muchomenu/list'] = array(
      'title' => 'List menus',
      'type' => MENU_DEFAULT_LOCAL_TASK,
      'weight' => -10,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );
  $items['admin/build/menu/muchomenu/add'] = array(
      'title' => 'Add menu',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_edit_menu', 'add'),
      'type' => MENU_LOCAL_TASK,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );
  $items['admin/build/menu/muchomenu/settings'] = array(
      'title' => 'Settings',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_configure'),
      'type' => MENU_LOCAL_TASK,
      'weight' => 5,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );
  $items['admin/build/menu/muchomenu-customize/%muchomenu'] = array(
      'title' => 'Customize menu',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_overview_form', 4),
      'title callback' => 'muchomenu_overview_title',
      'title arguments' => array(4),
      'access arguments' => $admin_access,
      'type' => MENU_CALLBACK,
      'file' => 'includes/admin.inc',
  );
  $items['admin/build/menu/muchomenu-customize/%muchomenu/list'] = array(
      'title' => 'List items',
      'weight' => -10,
      'type' => MENU_DEFAULT_LOCAL_TASK,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );
  $items['admin/build/menu/muchomenu-customize/%muchomenu/add'] = array(
      'title' => 'Add item',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_edit_item', 'add', NULL, 4),
      'type' => MENU_LOCAL_TASK,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );
  $items['admin/build/menu/muchomenu-customize/%muchomenu/edit'] = array(
      'title' => 'Edit menu',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_edit_menu', 'edit', 4),
      'type' => MENU_LOCAL_TASK,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );
  $items['admin/build/menu/muchomenu-customize/%muchomenu/delete'] = array(
      'title' => 'Delete menu',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_delete_menu_confirm', 4),
      'type' => MENU_CALLBACK,
      'file' => 'includes/admin.inc',
      'access arguments' => $admin_access,
  );


  $items['admin/build/menu/muchomenu/item/%muchomenu_link/edit'] = array(
      'title' => 'Edit menu item',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_edit_item', 'edit', 5, NULL),
      'type' => MENU_CALLBACK,
      'access arguments' => $admin_access,
      'file' => 'includes/admin.inc',
  );
  $items['admin/build/menu/muchomenu/item/%muchomenu_link/delete'] = array(
      'title' => 'Delete menu item',
      'page callback' => 'drupal_get_form',
      'page arguments' => array('muchomenu_item_delete_form', 5),
      'type' => MENU_CALLBACK,
      'access arguments' => $admin_access,
      'file' => 'includes/admin.inc',
  );

  return $items;
}

/**
 * Implements hook_perm().
 */
function muchomenu_perm() {
  return array('administer muchomenu');
}

/**
 * Implements hook_block().
 */
function muchomenu_block($op = 'list', $delta = 0) {
  $menus = muchomenu_get_menus();
  if ($op == 'list') {
    $blocks = array();
    if (!empty($menus)) {
      foreach ($menus as $id => $title) {
        $blocks[$id]['info'] = check_plain($title);
        $blocks[$id]['cache'] = BLOCK_NO_CACHE;
      }
    }
    return $blocks;
  }
  elseif ($op == 'view') {
    // $delta represents the menu id
    $menu = muchomenu_load($delta);
    $menu_tree = muchomenu_tree_data($delta, FALSE);
    $data['subject'] = check_plain($menus[$delta]);
    $data['content'] = theme('muchomenu_menu_tree', $menu, $menu_tree);
    return $data;
  }
}

/**
 * Implements hook_theme().
 */
function muchomenu_theme() {
  return array(
      'muchomenu_overview_form' => array(
          'arguments' => array('form' => NULL),
          'file' => 'includes/theme.inc',
      ),
      'muchomenu_item' => array(
          'arguments' => array('item', 'has_children', 'prefix' => '', 'layout' => 'vertical', 'menu' => ''),
          'file' => 'includes/theme.inc',
      ),
      'muchomenu_tree' => array(
          'arguments' => array('tree' => NULL),
          'file' => 'includes/theme.inc',
      ),
      'muchomenu_menu_tree' => array(
          'arguments' => array('menu' => null, 'tree' => array()),
          'file' => 'includes/theme.inc',
      ),
  );
}