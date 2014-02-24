# Current Migration Steps to 4.3

The following are the steps which are potentially necessary for the migration. We would like to reduce and make migration simpler, but in reality that's what we have.

If any steps over here can be omitted - they please create issue in bug tracker and add respective note.

## Different Configurations
Agile Toolkit 4.3 can operate in different ways. By default your project folder would contain several "interfaces" - admin, frontend, api etc. Previously atk4 was designed to work with one interface at a time.  

Decide which environment is most suitable for your current project.

### Backward-compatible setup

You should be able to use 4.3 version with EXACTLY THE SAME folder structure:

    my project
     - atk4
     - templates
     - lib
     - page
     - vendor
     
Note that there is no public folder. A current folder is considered "public". There must be no local project changes to make this structure work.

TODO: Create a test-environment for this.

### Single-interface setup with public

Agile Toolkit must detect and work in following environment:

    my project
      - config.php
      - templates
      - lib
      - page
      - public
      - public/atk4  -> ../vendor/atk4/atk4/public/atk4
      - vendor
      - vendor/atk4/atk4

In this single-instance setup, Agile Toolkit must work properly. There must be no local project changes to make this structure work.

### Multi-interface setup with optional sandbox

Agile Toolkit must be able to detect the following environment:

    my project
     - config.php
     - shared
     - shared/addons
     - shared/lib
     - shared/templates
     - frontend/config.php
     - frontend/lib
     - frontend/page
     - frontend/public/
     - frontend/public/atk4 ../../vendor/atk4/atk4/public/atk4
     - vendor/atk4/atk4
     - [optional] atk4-sandbox.phar

This setup must work with no project changes. If sandbox is detected, it must be included and Agile Toolkit will go into Development Mode.


## Installation instructions

I have broken down installation instructions into multiple parts. Please discuss them separately.

### Folder Structure

1. Select the most appropriate supported folder structure.
2. If you have any manual pathfinder modifications, remove them and refer to [TODO MUST LINK] pathfinder documentation on re-creating them.
3. Make sure "public" folder exists and contains "atk4" folder. In most cases, you will need to create either a symlink or Apache Alias.
4. Replace "index.php" with the compatible one. Agile Toolkit 4.3 uses "chdir" to position itself in the "interface" directory, while older Agile Toolkit would typically remain inside "public" folder. Here is example structure:

    <?php
    chdir('..');
    include 'vendor/autoload.php';
    $api = new Frontend('<your_project_name>');
    $api->main();

5. create composer.json in your project folder. You will use that to install 3rd party add-ons.

    {
        "autoload": {
            "psr-0": {"": "lib/"}
        },
        "repositories": [
            {
            "type": "vcs",
            "url": "git@github.com:atk4/agiletoolkit-css.git"
            }
        ],
        "require": {
            "atk4/atk4": "4.3.*@dev",
            "atk4/agiletoolkit-css": "dev-master"
        }
    }

6. copy atk4/tools/update.sh [TODO move sandbox's update into atk4] to your project folder. This is now universal script to update all of your project files.
7. remove atk4 and vendor folders if you have them
8. run `./update.sh`, which will create vendor folder and install dependencies


## Using Development Environment

If you are willing to use our Development environment with your existing project, you must download atk-sandbox.phar and place it inside your project folder. [TODO: link to file].

Agile Toolkit will detect this and will automatically go into development mode. Otherwise your project will operate in "production" mode.

If you are using development mode, you must also make sure the files are writable to PHP. The best way to do that is make sure that PHP runs with the same user as the one editing the files. This is to ensure that Agile Toolkit can update your settings for you.

Typically with Windows and Mac OSX MAMP that is already the case. For Linux the easiest is to use PHP in web-server mode [TODO - provide instructions]. A command-line to emulate web server.

For the first time - Agile Toolkit will also ask you to register your copy to keep sure you can install Add-Ons and communicate with agiletoolkit.org. This is a mandatory requirement for atk-sandbox.

## Using Custom CSS Theme

Normally Agile Toolkit distribution contains a standard Agile Toolkit CSS file. If you want to use custom CSS theme in your project, you'll need to install LESS compiler and set up Agile Toolkit CSS build files.

1. Use either UI-based LESS compiler or install "lessc" with NPM. [TODO - link to doc]
2. Use interactive theme builder on www.agiletoolkit.org, download "agiletoolkit-css" folder.
3. Copy files from agiletoolkit-css into "interface"/public folder.
4. In the future if you need to upgrade, only replace interface/public/css/libs folder contents.
5. Modify public/css/style.less and compile it with "lessc".

## Application-related changes

There are number of changes inside PHP, which you might need to address. If you are not using any of the advanced features, you might be ok.

### Compatibility layer

Agile Toolkit has a compatibility controller, which can be activated like this:

    $this->add('Controller_Compat42');
    
Using this you can enable number of backwards-compatible features including:

 - useOldStyle - tries to use old templates for objects.
 - TODO, please suggest other "compatible" modes.

### Layout

Although Agile Toolkit can operate in compatible mode, you are recommended to use "Layout". That's a new intermediate component between "shared.html" and Page which will contain menus, sidebars, headers and footers. You initialize layout in api->layout.

If you switch to using Layout, you must remember to no longer use $api->add('View'). Here is suggested syntax:

    $l = $this->api->layout?:$this->api;
    $l->add('Menu',null,'Menu');

It's very likely that you already use Menu like that.

TODO: place link to Layout documentation.

### API and Page Properties

Please set $title for your application (Frontend) and also for your pages. This make sure that your `<title>` property is always used nicely.

TODO: place link to breadcrumb documentation.

### Converting Templates

In Agile Toolkit 4.3 we have switched to new template tag style. Previously <?$foo?> is now used as {$foo}. You can still use {$foo} and {/}.

Go into your "templates" folder and execute `../path/to/atk4/tools/upgrade_templates.sh`. This will convert your existing template into new format. Don't forget to backup.

### CSS Changes

Please remember that CSS for 4.3 is not compatible with 4.2 and if you had some custom templates, you might need to change them.

The biggest change is probably Grid system. Previously .atk-row .span4 is now .row .col.span_4


## HEEEEEELP!!

Any sections missing? Please click [EDIT] on this file and add empty-sections. I'll provide the contents.


## OLD INSTRUCTIONS

TODO: konstantin, please review and remove, but ONLY when new instructions are sufficient and are better.

 


1. Delete ATK folder.
2. Create “<root_folder>/shared” folder.
3. Move “Model” folder from ‘<root_folder>/lib’ to ‘<root_folder>/shared’ folder.
4. Create ‘<root_folder>/public’ folder.
5. Create <root_folder>/public/index.php with the following code:
	<?php
chdir('..');
include 'vendor/autoload.php';
$api = new Frontend('<your_project_name>');
$api->main();
6. Edit ‘<root_folder>/index.php’:
	<?php
header('Location: public/');
7. Create or copy ‘<root_folder>/update.sh’ file.
8. Create or copy and edit ‘<root_folder>/composer.json’ file:
{
    "autoload": {
        "psr-0": {"": "lib/"}
    },
    "repositories": [
        {
        "type": "vcs",
        "url": "git@github.com:atk4/agiletoolkit-css.git"
        }
    ],
    "require": {
        "atk4/atk4": "4.3.*@dev",
        "atk4/agiletoolkit-css": "dev-master"
    }
}
9. Run ‘<root_folder>/update.sh’ in console.
10. Create folder ‘<root_folder>/public/css’
11. Create file in ‘<root_folder>/public/css.style.less’ fir the following code:
	@import "../../vendor/atk4/agiletoolkit-css/framework/css/agiletoolkit.less";
12. Run lessc
13. Create a simlink to the folder ‘font’:
../vendor/atk4/agiletoolkit-css/framework/font
14. Move your custom js folder to ‘<root_folder>/public/js or create a simlink to it.
15. Set up Pathfinder like so:
	$this->pathfinder->addLocation(array(
            	'php'=>array('shared'),
))->setBasePath('.');
16. Add default layout into ‘<root_folder>/lib/Frontent.php’.
	$this->layout = $this->add('Layout_Fluid');
17. Now you have to connect all your “View” objects to this layout
18. А дальше трахайтесь по обстоятельствам :)

Now you can run the project.

If you have custom templates do next steps
1. Move your template file and folders from ‘<root_folder>/templates/default’ to the ‘<root_folder>/templates/’.
2. I recommend to not use shared.html file at all.
3. Open console in the folder ‘<root_folder>/templates/’ and run script. It will change template tags from <??> to {}:
	‘../vendor/atk4/atk4/tools/upgrade_templates.sh’
5. Move your custom css files to ‘<root_folder>/public/css’
6. Move your custom js files to ‘<root_folder>/public/js’
7. Connect your custom css to the  ‘<root_folder>/public/css/style.less’
