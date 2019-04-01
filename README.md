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

## Drupal 8 Theming - Part 02 - Disable Cache, Enable Twig Debug
https://www.youtube.com/watch?v=rRsOxSuJ4OU

* Enable twig debugging
* Disable Drupal cache

After creating a new theme, there will still be some sort of template that is being used. Where does it come from?

If you create a new theme and you have content, but there isn’t a template for that, Drupal will default to its core template. This can be found in `core > modules >  system > templates > page.html.twig`.

* Copy and paste this file into new theme folder.

ℹ️ Twig: templating language that Drupal uses to display information on your pages 

If you add content to `page.html.twig` in your template folder, the changes will not show unless you clear the cache `Configuration > Performance > Clear all caches`

### How to disable Drupal cache (for development purposes):

* Go to sites folder where you will find `example.settings.local.php`

* Use file manager to copy `example.settings.local.php` into folder `sites > default` folder (which is a protected folder, so you’ll need to authenticate).

* Rename `example.settings.local.php` to `settings.local.php`.

* In `settings.local.php`, uncomment 
```
$settings['cache']['bins']['render'] = 'cache.backend.null';
```

* Go to `settings.php` (in default folder) and uncomment:
```
if (file_exists($app_root . '/' . $site_path . '/settings.local.php')) {
  include $app_root . '/' . $site_path . '/settings.local.php';
}
```
👆 At the end of the file. This file is protected as well so you’ll need to select ‘overwrite’ when prompted.

* Then visit https://drupal.test/core/rebuild.php to rebuild the site configuration.

* Go to `default.services.yml` (in default folder), you can see there that 
```
debug: false
auto_reload: null
cache: true
```

We don’t want to change it in this file because we want to set up a local dev environment (separate dev from prod env).

### To set up a local dev environment:

So, go to `development.services.yml` (in the sites folder), and add the following (from `default.services.yml`):
```
parameters:
  twig.config:
    debug: true
    auto_reload: true
    cache: false
```

* You’ll have to manually clear cache once more in the CMS.

* If you get an error in the browser, rebuild the site configuration again.

* To test if twig debug is working, open code inspector, where you will see `<!-- THEME DEBUG -->` comments. You will also find that you no longer have to manually clear cache.

## Drupal 8 Theming - Part 03 - Gulp.js, Sass, LiveReload
https://www.youtube.com/watch?v=vatnNkOKZ7o

https://github.com/ivandoric/d8theming/releases/tag/3

## Add assets to project (css, js, gulp)

* Download assets from Olympos repo (https://github.com/ivandoric/olympos).

* Copy `gulpfile.js` and `package.json` from the assets folder and paste in project folder (inside the folder that contains the `themes` folder, not the `themes` folder).

* Copy `images`, `js`, `lib`, and `sass` folders from assets folder and paste in to belbox folder.

* Delete `olympos.min.js` from (copied) js folder.

* Commit new files

## Update files to correspond to Drupal installation + theme

### style.scss

* Delete wordpress comments from style.scss file.

### gulpfile.js

* Rename paths: replace `./wp-content/themes/olympos` with `./themes/custom/belbox`

* In sass task: change `.pipe(gulp.dest('./themes/custom/belbox'));` to `.pipe(gulp.dest('./themes/custom/belbox/css'));` (as defined in libraries file earlier)

* In uglify task: rename `olympos.min.js` to `main.js` (as defined in libraries file earlier)

* In watch task:  
  * add css folder
  * watch twig files not php files
  * remove , './themes/custom/belbox/parts/**/*.php'
i.e., 
```
gulp.watch(['./themes/custom/belbox/style.css', 't/themescustom/belboxs/*.php', 't/themescustom/belboxs/js/*.js', './themes/custom/belbox/parts/**/*.php'], function (files){
        livereload.changed(files)
    });
```
to 
```
gulp.watch(['./themes/custom/belbox/css/style.css', 't/themescustom/belboxs/*.php', 't/themescustom/belboxs/js/*.js', './themes/custom/belbox/parts/**/*.php'], function (files){
        livereload.changed(files)
    });
```

* Install modules required by gulp file. Go to terminal and run `npm install` in project folder.

* Run `gulp watch`

* To test if sass is working, go to a sass file, e.g., `globals.scss`, make a change and save. If it works a css folder will be created in belbox with `style.css` and `style.css.map` in it.

* To test if `LiveReload` works, go to browser, start `LiveReload` plugin for Chrome. Change something in `_globals.scss`, go to site in browser and see if it updates automatically.

* To test if `LiveReload` is working for twig files, change something in `page.html.twig`.

* The `js` folder is currently empty, which causes a 404 error in the console. To test if js is working, go to `main.js` in `lib`, make a change and save. If it works, a `main.js` file will be added to the `js` folder. You can add other files to the `lib` folder and gulp will concatenate and uglify them into `js/main.js`.

