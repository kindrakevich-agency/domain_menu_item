# Domain Menu Item

## Description

The Domain Menu Item module allows you to associate menu items with specific domains when using the Domain module in Drupal 11. This enables you to control which menu items appear on which domains, providing better multi-site menu management.

## Features

- Add a domain selection field when editing menu items
- Associate menu items with one or more domains
- Automatically filter menu items based on the active domain
- Show menu items on all domains if no specific domain is selected
- Clean up domain associations when menu items are deleted

## Requirements

- Drupal 11.x
- Domain module (drupal/domain)

## Installation

1. Download and extract the module to your `modules/custom` directory
2. Enable the module using Drush:
   ```bash
   drush en domain_menu_item
   ```
   Or enable it through the Drupal admin interface at `/admin/modules`

3. Clear the cache:
   ```bash
   drush cr
   ```

## Usage

1. Navigate to the menu administration page (`/admin/structure/menu`)
2. Select a menu and add or edit a menu item
3. You will see a new "Show on domains" field with checkboxes for each domain
4. Select the domain(s) where this menu item should appear
5. Leave all checkboxes unchecked to show the menu item on all domains
6. Save the menu item

The menu item will now only appear on the selected domains.

## How It Works

- **Form Alter**: Adds a domain selection field to the menu link content form
- **Database Storage**: Stores domain associations in a custom `domain_menu_item` table
- **Menu Filtering**: Uses `hook_preprocess_menu()` to filter menu items on frontend pages based on the active domain
- **Admin Access**: Admins see ALL menu items in admin pages - filtering only applies to frontend
- **Cleanup**: Automatically removes domain associations when menu items are deleted

## Database Schema

The module creates a `domain_menu_item` table with the following structure:

- `id`: Primary key
- `menu_link_content_id`: Reference to the menu link entity
- `domain_id`: The domain ID from the Domain module

## Recent Fixes

### v1.1 - Fixed Intermittent Menu Item Disappearing Issue

**Problem**: Menu items were sometimes disappearing intermittently after cache clears.

**Root Cause**: The module was using `hook_menu_links_discovered_alter()` which only runs during menu cache rebuild, not on every page load. This caused:
- Menu filtering to work correctly immediately after cache clear
- But filtering to fail on subsequent page loads
- Intermittent behavior depending on cache state

**Solution**:
- Replaced `hook_menu_links_discovered_alter()` with `hook_preprocess_menu()` which runs at render time on every page load
- Added admin route detection to skip filtering on admin pages (admins see all menu items)
- Added proper cache management with cache tags for better performance
- Added cache invalidation when domain assignments change
- Menu filtering now works consistently on every frontend page load

## Troubleshooting

### Menu items not appearing/disappearing correctly

1. Clear the cache: `drush cr`
2. Rebuild menu cache: `drush cache:rebuild menu`
3. Check that the Domain module is properly configured

### Domain selection field not showing

1. Ensure the Domain module is enabled and configured
2. Verify that at least one domain exists in the system
3. Clear the cache

## Uninstallation

When you uninstall the module, the `domain_menu_item` table will be automatically dropped, removing all domain associations.

## Support

For bug reports and feature requests, please use the issue queue.

## License

This module is licensed under the GPL v2 or later.

## Author

Custom module for Drupal 11 domain-based menu management.
