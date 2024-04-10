# Kalamuna Recipes

**Work in Progress** This is a work in progress.  It is not stable or useful yet.

[Recipes](https://www.drupal.org/project/distributions_recipes) allow the one import of a set of configuration to a Drupal website.  After the import is complete, the recipe no longer does anything with the site, and it is up to the site maintainer to manage the site from there.  This is similar to the new [starterkit theme](https://www.drupal.org/docs/core-modules-and-themes/core-themes/starterkit-theme) concept introduced in Drupal 10.

## Purpose

This repo is intended to hold a collection of Kalamuna site-starter configurations, that will build on each other.  We would have a starter that brings in the configurations that are common to all of our sites, and then more specific configurations for different types of site.  As an example, we might have a non-profit fundraiser config that brings in the modules and config that we commonly need when building a site that needs to help an organization fundraise.

### KalaStarter
The starter recipe for Kalamuna installs several modules, and imports some or all of the default configuration that comes with them.  Most notably, it sets of the default config split environments, and configures pathauto for node paths.  It currently installs the following recipes:

  #### Core.
  - automated_cron
  - block_content
  - ckeditor5
  - config
  - dblog
  - dynamic_page_cache
  - field_ui
  - layout_builder
  - menu_ui
  - options
  - page_cache
  - path
  - path_alias
  - taxonomy
  - views_ui
  #### Contrib.
  - admin_toolbar
  - better_exposed_filters
  - config_split
  - ctools
  - environment_indicator
  - environment_indicator_ribbon
  - google_tag
  - pathauto
  - rabbit_hole
  - search_api
  - simple_sitemap
  - token
  - ultimate_cron

## How to Use

Refer to the [Getting Started Guide](https://git.drupalcode.org/project/distributions_recipes/-/blob/1.0.x/docs/getting_started.md) for the latest information on how to use recipes, but as of this writing, the steps below should suffice for installing the kalastarter recipe.

1. Create a new Drupal website.
2. Run the following commands to patch your site for use with recipes.
```
# Configure Composer Patches.
composer config allow-plugins.cweagans/composer-patches true
composer require cweagans/composer-patches

# Add Drupal core patch for recipes to composer.json and update core.
composer config extra.patches --merge --json '{"drupal/core": {"Allow recipes to be applied": "https://git.drupalcode.org/project/distributions_recipes/-/raw/patch/recipe-10.2.x.patch"}}'
composer update drupal/core

# Configure the location recipes go when required.
composer config allow-plugins.oomphinc/composer-installers-extender
composer config extra.installer-types --merge --json '["drupal-recipe"]'
composer config extra.installer-paths --merge --json '{"web/recipes/contrib/{$name}": ["type:drupal-recipe"]}'
composer require oomphinc/composer-installers-extender

# Requires Drupal Recipe Unpack.
composer config allow-plugins.ewcomposer/unpack true
composer config repo.recipe-unpack vcs https://gitlab.ewdev.ca/yonas.legesse/drupal-recipe-unpack.git
composer require ewcomposer/unpack:1.x-dev@dev
```

If using a local development environment such as lando or ddev, it will be easier to use the commands above if you "ssh" into the web container first (e.g. `ddev sssh` or `lando ssh`).

3. copy the `kalastarter` directory into the `web/recipes/custom` directory on your site.  You may need to create some of those directories
4. If you are on ddev, and want to make it easier to run the drupal script command (used for recipes, starterkit, and other things) copy the `drupal` command in the "ddev command/web" directory into either `~/.ddev/commands/web` (for a global install) or the `.ddev/commands/web` in your ddev project for a local install.
5. Install your site.  The minimal profile starts out with the least configuration in place. (`drush si minimal`)
6. Install the recipe.  Either ssh into your web container and run:

`php core/scripts/drupal recipe recipes/custom/kalastarter`

or if you have the ddev command from step 4 just run:

`ddev drupal recipe recipes/custom/kalastarter`

7. export your configuration, to get the results of installing the recipe checked into code.
