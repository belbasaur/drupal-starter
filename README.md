# drupal-starter
Drupal theming starter

## Drupal 8 Theming - Part 00 - Installing Drupal 8
https://www.youtube.com/watch?v=XOV8VNTAvek

Drupal downloaded from: https://www.drupal.org/node/2614756 


## Drupal 8 Theming - Part 01 - Theme Setup
https://www.youtube.com/watch?v=a9u0B1F650U

* Configure theme (Set up & activate theme)
* Set up CSS & JS (Add and remove)

You can see the themes by going to `Manage > Appearance` in the CMS.

### Add a theme:

* Open project folder in text editor.

* Create new directory called ‘custom’ in `themes` folder (`project > themes`). 

* Inside custom folder, create new folder for your custom theme, e.g. `belbox`

* In your new folder, create file called, `<theme-name>.info.yml` i.e., `belbox.info.yml`

* Add the following info in `belbox.info.yml`:

```
name: Belbox
description: Drupal 8 Starter Theme
type: theme
core: 8.x
```

👆 `core` defines drupal installation.

* If you now go to CMS Appearance, you’ll see your theme as an uninstalled theme. Click on ‘Install and set as default’ to start using theme.

### Add and remove some CSS:

Objective: have a clean theme, without a lot of CSS bogging us down. If you inspect source right now you’ll see a whole heap of CSS that is not doing anything. Added by Drupal by default.

If you’re not seeing it, in CMS, go to `Manage > Performance > Bandwidth Optimisation`, and untick the checkboxes:
`Aggregate CSS files`
`Aggregate JavaScript files`

Log out of CMS to see all the CSS that are NOT used for the Drupal toolbar.

#### To remove CSS:

* Go to `belbox.info.yml` file, add:
```
stylesheets-remove:
 - core/themes/stable/css/views/views.module.css
 - core/themes/stable/css/system/components/align.module.css
 ```

ℹ️ If you don’t see changes, clear cache as follows:
In CMS `Manage > Configuration > Performance > Clear cache > Clear all caches`

#### To add CSS:

* Create new file in belbox folder called `belbox.libraries.yml`

* In `belbox.libraries.yml`, add:
```
global-css:
 css:
   theme:
     css/style.css: {}
```

* Also add to info file (`belbox.info.yml`):
Below core:
```
libraries:
 - belbox/global-css
```

#### To add JS:

* In `belbox.libraries.yml` add:
```
global-js:
 js:
   js/main.js: {}
```

* To define dependencies, e.g. jQuery
```
global-js:
 js:
   js/main.js: {}
 dependencies:
   - core/jquery
```

👆 Core because Drupal comes with jQuery

* Then in info file:
```
name: Belbox
description: Drupal 8 Starter Theme
type: theme
core: 8.x
libraries:
 - belbox/global-css
 - belbox/global-js
stylesheets-remove:
 - core/themes/stable/css/views/views.module.css
 - core/themes/stable/css/system/components/align.module.css
```

ℹ️ You’ll find the js in the source before the closing body tag.
