# ATK4 Addon Structure
#### February 2014

## General information

* ATK4 Addon is a Composer package. To become a composer package it needs to contain the file called <a href = "https://getcomposer.org/doc/01-basic-usage.md#composer-json-project-setup">composer.json</a> located in root directory of addon and can be registered on <a href = "https://packagist.org/">packagist.org</a>. 
* ATK4 addon doesn’t use composer autoload because ATK4 has its own autoload pathfinder. 
* To enable your addon to be installed from sandbox UI it needs to be registered in AgileToolkit Market. For manual installation you need to add required line to composer.json file of your project.


## Autoload

Each addon has its own namespace. This will help to avoid class name duplication in addons from different vendors. Namespace structure is the following: <b>atk4\blog</b>, where

* <b>atk4</b> is a vendor name on github
* <b>blog</b> is a repository name on github

If you have class <b>Model_Article</b> in your addon you can load it in your project like this:

    $this->add(‘atk4/blog/Model_Article’);


## Initiator class

This class is used for initiation of your addon on every application call and is not required. You can use this class to perform some actions what your addon require. Your addon initiator must be inherited from Controller_Addon and must contain few fields


    namespace rvadym\blog;
    class Initiator extends \Controller_Addon {
    
       // this var is used to store initiator object in $this->api->addons array
       // in our case this initiator will be accessible like this $this->api->addons[‘rvadym_blog’]
       // it is resposibility of addon creator to make accessible other part of addon from Initiator
        public $api_var        = 'rvadym_blog';
    
    
       // addon name to be shown in admin part if sandbox is installed
        public $addon_name     = 'Agile Toolkit Blog Addon';
    
       // types of private files what ATK4 will try to find in your addon
        public $addon_private_locations = array(
            'docs'      => 'docs',
            'php'       => 'lib',
            'page'      => 'page',
            'template'  => 'templates',
        );
    
       // types of public files what ATK4 will try to find in your addon
        public $addon_public_locations  = array(
            'js'     => 'js',
            'css'    => 'css',
        );  
    
       // true is your addon contain some pages for Admin part 
        public $with_pages = true; 
    }



####Controller_addon::$api_var
This var is used to store initiator object in $this->api->addons array. In our case this initiator will be accessible like this $this->api->addons[‘rvadym_blog’]. It is resposibility of addon creator to make accessible other part of addon from Initiator.

####Controller_addon::$addon_name
addon name to be shown in admin part if sandbox is installed

####Controller_addon::$atk_version
suported ATK4 version

####Controller_addon::$namespace
namespace of this addon.

####Controller_addon::$addon_obj
addon object from config

####Controller_addon::$addon_private_locations
types of private files what ATK4 will try to find in your addon

####Controller_addon::$addon_public_locations
types of public files what ATK4 will try to find in your addon

####Controller_addon::$with_pages
TRUE if your addon contain pages for Admin part.


## Addon filesystem

Addon has almost same filesystem structure like regular atk4 project.It can contain following folders inside of addon root folder:

* <b>lib</b> - folder for all php classes based on atk4 <b>AbstractObject</b>.
* <b>templates</b> - templates for addon pages and UI elements.
* <b>public</b> - all files what should be visible from the web.

and following files:

* composer.json

Addon can have these files inside its lib directory:

* <b>Initiator.php</b> - class Initiator describes all actions what should be performed each time addon is called.



###Addon filesystem structure

          {your addon}↴
                      ↦ lib ———↴
                      |         ↦ Controller
                      |         ↦ Model
                      |         ↳ View
                      |
                      ↦ page
                      |
                      ↦ template↴ 
                      |          ↦ page
                      |          ↳ view
                      |
                      ↳public——↴
                                ↦ css
                                ↳ js




