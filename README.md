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
  * replace `*` with `main` in `t/themescustom/belboxs/js/*.js`
i.e., 
```
gulp.watch(['./themes/custom/belbox/style.css', 't/themescustom/belboxs/*.php', 't/themescustom/belboxs/js/*.js', './themes/custom/belbox/parts/**/*.php'], function (files){
        livereload.changed(files)
    });
```
to 
```
gulp.watch(['./themes/custom/belbox/css/style.css', 't/themescustom/belboxs/*.php', 't/themescustom/belboxs/js/main.js', './themes/custom/belbox/parts/**/*.php'], function (files){
        livereload.changed(files)
    });
```

* Install modules required by gulp file. Go to terminal and run `npm install` in project folder.

* Run `gulp watch`

* To test if sass is working, go to a sass file, e.g., `globals.scss`, make a change and save. If it works a css folder will be created in belbox with `style.css` and `style.css.map` in it.

* To test if `LiveReload` works, go to browser, start `LiveReload` plugin for Chrome. Change something in `_globals.scss`, go to site in browser and see if it updates automatically.

* To test if `LiveReload` is working for twig files, change something in `page.html.twig`.

* The `js` folder is currently empty, which causes a 404 error in the console. To test if js is working, go to `main.js` in `lib`, make a change and save. If it works, a `main.js` file will be added to the `js` folder. You can add other files to the `lib` folder and gulp will concatenate and uglify them into `js/main.js`.

## Drupal 8 Theming - Part 04 - Blocks and Regions
https://www.youtube.com/watch?v=DqTDiLzr4Iw

* Explain what blocks and regions are
* Make regions and put blocks into those regions

```
"In this Drupal 8 Theming tutorial we are going to be looking at setting up Drupal regions and adding blocks of content to them.

Drupal 8 and all the other "Drupals" have something called regions, they are locations on your page where you can put pieces of content or functionality. You do that among other things by adding blocks to them.

Blocks are "containers" that contain some content or functionality in them. You can easily move them around the regions by just dragging them in Drupal administration. Pretty great stuff."
```

### View existing blocks

* In CMS, go to structure > block layout

* Click on `Demonstrate block regions` to view block regions

ℹ️ Blocks = pieces of content you can put on your page

In the `page.html.twig` template you can see the regions, e.g. `{{ page.content }}`

This corresponds to the regions we have in the CMS.

### To override default regions / create custom regions

* Go to info file `belbox.info.yml`

* Define regions as follows:
```
regions:
  content: 'Main Content'
  header: 'Header'
  footer: 'Footer'
  sidebar: 'Sidebar'
```

ℹ️ When you make changes to the configuration file, you have to clear the cache to see the changes

* Default regions should now be disabled.

* Define regions created in the info file in the `page.html.twig` template, e.g.:

```
  <header role="banner">
    {{ page.header }}
  </header>

```

* Now you can drag and drop blocks into the defined regions in the CMS.

* You can also create new custom blocks and add them to your regions in the CMS using the `place block` button.

## Drupal 8 Theming - Part 05 - Let's Update Drupal 8
https://www.youtube.com/watch?v=MlLq05X5xKw

## Drupal 8 Theming - Part 06 - Theming the Header
https://www.youtube.com/watch?v=jLlXR-ONyy8

### Define homepage / frontpage

* Currently `page.html.twig` is used, no matter what type of content we go to.

* We want the front page to be different from all the other pages.

* If you open inspector, it gives file suggestions:
```
<!-- FILE NAME SUGGESTIONS:
   * html--front.html.twig
   * html--node.html.twig
   * html.html.twig
-->
```

* We will name our file `html--front.html.twig`.

* You can also see what template is currently being used to display this page:
```
<!-- BEGIN OUTPUT from 'core/themes/stable/templates/layout/html.html.twig' -->
```

* Rename `page.html.twig` to `html--front.html.twig`

ℹ️ NOTE: `html--front.html.twig` does not work, so I changed it to `page--front.html.twig`
https://www.drupal.org/docs/8/theming/twig/twig-template-naming-conventions

* Clear cache (In CMS, go to configuration > Performance > Clear Cache)

### Create header for front page

* If you inspect the header you will find:
```
<!-- BEGIN OUTPUT from 'core/themes/stable/templates/block/block--system-branding-block.html.twig' -->
```

* I.e., In the CMS, if you go to `structure > Block layout`, you will see the header region is calling the block `Site branding`

* We want to overwrite `block--system-branding-block.html.twig` (as well as customise other core templates)

* Locate the file: `core > themes > stable > templates`

* Copy the templates folder and paste in theme folder (`belbox`)

ℹ️ Everytime you create a new file you need to clear the cache.

* Now you will find that the branding is being called from 
```
<!-- BEGIN OUTPUT from 'themes/custom/belbox/templates/block/block--system-branding-block.html.twig' -->
```

#### Let’s customise the header:

* Start `gulp watch` in terminal to do some CSS and turn on live reload in browser

* Go to `page--front.html.twig`

* Take the header out of layout container and place it above.

* Add a container to the header

* Add a class `main-header` to the header

* Add some styles to `.main-header`

* If you inspect the logo you will see that it calls `https://drupal.test/themes/custom/belbox/logo.svg` but we want it to get the logo from the images folder - we do not want to put images in the theme root.

* Open the file `block--system-branding-block.html.twig` file in the `templates > block` folder 

ℹ️ 
Twig:
`{% Do something %}`
`{{ Print something }}`
ℹ️ 

* To change the path that is used for the logo:
`<img src="{{ site_logo }}" alt="{{ 'Home'|t }}" />`

* In the CMS, go to `Appearance`
* Click on the theme’s `Settings` link
* Go to `Global settings` tab
* Untick `Use the logo supplied by the theme`
* Define the path to the custom logo: `themes/custom/belbox/images/logo.svg`
* Save configuration

* Style logo to make it smaller

* To remove the site name, go to the `Site branding` block and click on `Configure` and uncheck `Site name`

## Drupal 8 Theming - Part 07 - Adding Main Navigation
https://www.youtube.com/watch?v=KSsFt9rSNdU

### First of all you need to add content to link to.

* In the CMS, go to `Content > Add content > Basic page`

* Create Basic page called ‘Movies’ and click `Save`. Add two more basic pages for ‘About’ and ‘Contact’.

### Add pages to a menu

* Go to `Structure > Menus`

* Click on ‘Edit menu’ for the `Main navigation` menu

* Add a link to ‘Movies’, ‘About us’, and ‘Contact’

* Click and drag links to reorder in menu

* Every type of content in Drupal is called a node and will be referenced by an id

* To add the menu to the header region, go to `Structure > Block layout` and drag `Main navigation` into the header region and `Save blocks`

### Create a new region for the menu

* Go to `belbox.info.yml` and create a new region called `main_navigation`:
```
main_navigation: ‘Main Navigation’
```

* Clear cache

* In the `block layout` section of the CMS, and drag the main navigation block into the main navigation region

* Define the main navigation region on the front page header
```
<div class="main-navigation">
    {{ page.main_navigation }}
  </div>
```

* Style .main-navigation to look like a nav

## Drupal 8 Theming - Part 08 - Creating Partials
https://www.youtube.com/watch?v=RJrGumCYB_Y

# Add a reusable header to subpages

ℹ️ Partials: small blocks of code that you can reuse on your pages

* If you inspect a subpage you can see what template it is using by inspecting it with the developer tools:
```
<!-- BEGIN OUTPUT from 'themes/custom/belbox/templates/layout/page.html.twig' -->
```

* Locate the template file and delete the existing header.

* Create a new file for the reusable header and paste in the header code that was created for the homepage.

* Save the file as `header.html.twig` in a new folder called `partials`. This file should be in your theme folder (`belbox`), as a sibling of the templates folder. 

* To call the header, include `{% include directory ~ '/partials/header.html.twig' %}` above the container div in the frontpage and subpage templates.
