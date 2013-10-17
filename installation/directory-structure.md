# Installation > Directory Structure

## Installing By Download

If you install by download, the default directory structure will be:

    www/ <- Your webroot

    myapp/ <- Your app

        atk4/ <- Agile Toolkit core

        atk4-addons/ <- Agile Toolkit Addons

        lib/ <- Your class library

            Frontend.php <- Your apiFrontend app class
            Model/ <- Your Models
            View/ <- Your Views

        page/ <- Your Page Classes

        doc/ <- Your project documentation & SQL migration scripts

        templates/ <- Your project custom UI templates

        index.php <- Your application bootstrapping script

        config-default.php <- Your default configuration
        config-distrib.php <- Your per-installation sensitive configuration

## Installing by Composer

        ?? TODO

## Installing from GitHub repo
    TODO

----

----


Agile Toolkit is bundled with different types of files. In addition to PHP classes there are JavaScript libraries, HTML templates, CSS framework, Shell scripts and other assets. Let's go through your local installation and get familiar with file structure.

### Local files, atk4
If you have already looked inside the 'atk4' directory, you would have noticed that it has similar directories to your example application. When Agile Toolkit is looking for a certain file, it looks first in your "local" directory. If the file is not found there, then it looks inside the atk4 directory.

### atk4-addons
Agile Toolkit has many add-ons you can use. Addons are distributed through Agile Store in .ZIP files and are unpacked inside atk4-addons folder. Your installation should have created atk4-addons/helloworld and atk4-addons/sample_views for you. While some add-ons would only contain PHP classes, others may dynamically include additional resource types. To activate add-on, referr to the documetation of that specific add-on.

### Copy-On-Modify principle
Agile Toolkit requires minimally only one or two files. As you enhance or change the default behaviour of Agile Toolkit, you might want to change its files. You must never change files inside the 'atk4' folder. Instead, if they are PHP classes, you can just extend them. If it's impossible to achieve what you need through extending the class, you can force Agile Toolkit to use your own version of the class, image, stylesheet or HTML template: simply place a file with identical name outside the "atk4" folder. For example: by creating a file templates/default/images/logo.png you can replace the default logo with your own. However, never store an identical copy of a file without modification. If you do this, when Agile Toolkit updates the original, even when you update your installation, you will not see the changes.

### Resource Types
Agile Toolkit relies on the concept of "resource type" when it attempts to use a file. The most used resource types are:

php — class definitions which are not directly available, but are loaded automatically, when a class is used;

page — page classes, which can be accessed by specifying the page class name in the URL;

js — javascript includes;

template — files related to presentation, HTML, CSS and Images;

mail — mail templates.

The resource types are looked up in certain specific locations by default. However, there are ways for new locations to be added - ie, for storing shared content, or if introducing a new logical separation to your application.

### Essential files and folders
Nearly all the files and directories are optional. You shouldn't create them unless they are needed.

.htaccess           — contains mod_rewrite instructions.

atk4                — unmodified copy of Agile Toolkit.

atk4-addons         — unmodified copy of Agile Toolkit Addons.

config-default.php  — default configuration settings.

config-distrib.php  — template for config.php. This file is typically copied and renamed as config.php (see below).
                        
config.php          — install-specific configuration options, such as database connection settings, etc. WARNING: adding your site's config file to a public Version Control System is a security hazard.

doc                 — your software documentation, database initialization and update scripts.

index.php           — file containing the Agile Toolkit bootstrap code.

lib                 — your PHP code, php resource type.

page                — contains classes used for routing, "page" resource type.  test/foo.html would look for a page/test/foo.php file, and in it, a class named page_test_foo.

templates/          — technically, the only location apart from index.php to be accessible directly from the web.

templates/default   — overrides certain files of default template. Only place changed files here.

templates/js        — contains your own javascript plugins.

templates/mail      — mail templates.

upload              — webserver-writable directory for filestore add-on

### Essential files in atk4
You will notice, that the atk4 folder has a similar structure:

lib                 — Agile Toolkit PHP code, php resource type.

loader.php          — a file, which you must include in index.php.

templates/          — technically, the only location apart from index.php to be accessible directly from the web.

templates/default   — default HTML/CSS theme.

templates/elephant  - alternative HTML/CSS theme.

templates/shared    — shared components for all HTML/CSS themes.

templates/js        — JavaScript libraries including jQuery and jQuery UI.

templates/xslt      — some XSLT templates.

tools               — supplementary command-line utilities.

### Essential files in atk4-addons
The Addon directory contains additional classes and other resources. Typically, in the addon documentation you will find instructions on how to enable that addon. By default no resources are looked up in atk4-addons. You will need to add this inside the API init method:

    $this->addLocation('atk4-addons',array(
        'template'=>'misc/templates',
        'php'=>array('mvc',
            'billing/lib',
            'misc/lib',
            'crm/lib',
            )
        ))
        ->setParent($this->pathfinder->base_location);

misc/templates      — some additional HTML/CSS templates.

misc/lib            — small classes and views, which can be considered helpers. Feel free to either use them as they are or copy them into your local lib/ directory if you need to introduce modifications. If you contribute some useful classes, they will be added here.

misc/docs           — some universal resources, such as create script for 'country' table with up-to-date list of countries, filestore creation scripts. If you need to use them in your application, create symlink from inside your docs directory.

mvc                 — Model-View-Controller implementation. To be moved into atk4/ soon.

billing             — implementation of some billing processors