## STEPS 📔
Ensure you have created a drupal project and setup the site. <br>
Navigate to /web/modules and create a folder /custom/custom_block_name <br>
In the `/custom_block_name` folder add the following files
- [ ] custom_block.info.yml
- [ ] custom_block.module
- [ ] Create folder: `src/Plugin/Block/` and add file `custom_block.php`
> custom_block.info.yml
```
name: 'Custom Block Name'
type: module
description: 'Some description here...'
core: 8.x //must match the Drupal the site version
core_version_requirement: ^8 || ^9 || ^10 || ^11
package: Custom
dependencies:
  - drupal:block
```
> custom_block.php
```
<?php
  namespace Drupal\custom_block\Plugin\Block;
  use Drupal\Core\Block\BlockBase;
  use Drupal\Core\Block\BlockPluginInterface;

  class CustomBlock extends BlockBase implements BlockPluginInterface{
    /** * {@inheritdoc} **/
    public function build(){
      return ['#markup'=>$this->t('My custom block plugin'),
      ];
    }
  }
```
> Now navigate to the main project directory and enable the module `./vendor/bin/drush en custom_block -y`
## Setup in Drupal Site
Now in the drupal site go to `Block Layout` and "Place Block" the newly created block in a relavant area.

## Custom html.twig dynamic content
Create `/js` and `/css` folders in the root<br>
In the `.module` file add:<br>
```
<?php 
/** 
* Implements hook_theme()
*/
function custom_block_theme(){
  return [
    'custom_block'=>[
      'variables'=>['custom_data' => NULL,],
      'template'=>'footer',// This should match the Twig template file name.
    ],
  ];
}
```
Create a library file `custom_block.libraries.yml` with code:<br>
```
custom_library:
  version: 1.x
  css:
    theme:
      css/custom_block.style.css: {}
  js:
    js/custom_block.js: {}
  dependencies:
    - core/jquery
```
Create a folder `/templates` and a file `custom.html.twig` with code:<br>
```
<div>{{custom_data.message}}</div>
```
Now in the `custom_block.php` file:<br>
```
public function build(){
  return [
    '#attached' => [ //use this to integrate your css and js files.
      'library' => [
          'custom_block_name/custom_library', // Reference a custom library.
      ],
    ],
    '#theme' => 'custom_block', //must match name in .libraries file
    '#custom_data' => [
      'message' => 'Hello Footer',
    ],
  ];
}
```
## Enable the plugin
Open gitbash and run
```
./vendor/bin/drush en custom_block_name -y
./vendor/bin/drush cr
```
