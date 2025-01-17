## STEPS 📔
Ensure you have created a drupal project and setup the site. <br>
Navigate to /web/modules and create a folder /custom/custom_block_name <br>
For example, let's create an image carousel plugin <br>
In the `/image_carousel` folder add the following files
- [ ] image_carousel.info.yml
- [ ] image_carousel.module
- [ ] image_carousel.libraries.yml
- [ ] Create folder: `src/Plugin/Block/` and add file `image_carousel.php`
- [ ] Create folder `templates/` and add file `carousel_template.html.twig`
- [ ] Create folder `css/` and add file `image_carousel.css`
- [ ] Create folder `js/` and add file `image_carousel.js`
> image_carousel.info.yml
```
name: 'Image Carousel'
type: module
description: 'This is a custom image carousel for Drupal versions 8+'
core: 8.x //must match the Drupal the site version
core_version_requirement: ^8 || ^9 || ^10 || ^11
package: Custom
dependencies:
  - drupal:block
```
> image_carousel.php
```
<?php
  namespace Drupal\image_carousel\Plugin\Block; //namespace must match project folder name
  use Drupal\Core\Block\BlockBase;
  use Drupal\Core\Block\BlockPluginInterface;
  /**
     * Provides a 'My Custom Block' block.
     *
     * @Block(
     *   id = "image_carousel", //match project folder name.
     *   admin_label = @Translation("Custom Image Carousel"),
     *   category = @Translation("Custom") //this block will be found under "Custom"
     * )
     */

  class image_carousel extends BlockBase implements BlockPluginInterface{
    /** * {@inheritdoc} **/
    public function build(){
      return ['#markup'=>$this->t('My custom image carousel block.'),
      ];
    }
  }
```
## Custom html.twig dynamic content
> image_carousel.module
```
<?php 
/** 
* Implements hook_theme()
*/
function image_carousel_block_theme(){
  return [
    'image_carousel_block'=>[
      'variables'=>['custom_data' => NULL,],
      'template'=>'carousel_template',// This should match the Twig template file name.
    ],
  ];
}
```
> image_carousel.libraries.yml
```
image_carousel_library:
  version: 1.x
  css:
    theme:
      css/image_carousel.css: {}
  js:
    js/image_carousel.js: {}
  dependencies:
    - core/jquery
```
> carousel_theme.html.twig
```
<div>{{custom_data.message}}</div>
```
Now we can update the `image_carousel.php` file to have dynamic content:<br>
```
public function build(){
  return [
    '#attached' => [ //use this to integrate your css and js files.
      'library' => [
          'image_carousel/custom_library', // Reference a custom library in .libraries file.
      ],
    ],
    '#theme' => 'image_carousel_block', //must match name in .module file
    '#custom_data' => [
      'message' => 'Hello World, this is an image carousel',
    ],
  ];
}
```
## Important Notes
- [ ] The class name and the file name must be the same (class image_carousel and src/Plugin/Block/image_carousel.php). If the class name is different, the block will appear in the list of available blocks, however, you will not be able to add it.
- [ ] The namespace at the top of the block must match your module structure (the image_carousel.module will need namespace Drupal\image_carousel\Plugin\Block).
- [ ] Make sure your module's naming convention is all lowercase/underscores. Some users report blocks not showing for modules with camelCase naming convention. `image_carousel` instead of `imageCarousel`
## Setup in Drupal Site
Open gitbash and run
```
./vendor/bin/drush en image_carousel -y
./vendor/bin/drush cr
```
Now in the drupal site go to `Block Layout` and "Place Block" the newly created block in a relavant area.
