Before diving into PHP code, I would like you to look at the file structure of your application and understand how to locate files you need. 

Agile Toolkit also takes special care to distribute files into separate folders depending on how are they used or who wrote them.

## The Project Folder

When you downloaded Agile Toolkit archive and extracted it for the first time, you undoubtedly found an "agiletoolkit" folder which were instructed to place somewhere in your Web Server Documents folder. This is your Project folder.

While in Sandbox, your project folder must the WRITABLE by PHP. For Mac and Windows MAMP/WAMP environments it is already writable, but if you are using Linux workstation, you might need to change "User" setting for PHP to have access to your files. (Files are not writable on production server)

    admin/
    frontend/
    shared/
    
    agiletoolkit-sandbox.phar

    db/
    vendor/
    
    
    composer.json
    composer.lock
    composer.phar
    
    config-sandbox.php
    config-default.php
    dependencies
    
    index.php
    
### Interfaces (admin and frontend)

Interface is a sub-set of your project aimed at a specific type of use. By default your project contains "admin" for restricted administrative and "frontend" interface for public access. You may create more interfaces for testing, commant-line use, cron jobs, API or other web interfaces. 

Web interfaces must contain "public" folder, which will be used as a web-root on your production server. A document root of www.myproject.com would point to `/path/to/project/frontend/public`.

### shared

This folder contains resources which are shared between interfaces. Typically your Models will shared, however you may want to place your own "addons" and "templates" in shared folder.

### agiletoolkit-sandbox.phar

Agile Toolkit Sandbox in a development environment for Agile Toolkit. This archive contains the necessary code to operate Agile Toolkit on your local computer. This file contains communication tools between your project and agiletoolkit.org and necessary code to install add-ons, help you configure database, debug and find problems in your code. 

This archive also contains Agile Toolkit installer which you will see if you direct your browser to through web server to your project directory.

AgileToolkit Sandbox is NOT installed on the server when you publish. If you delete this file.

### db

Folder required by agiletoolkit-sandbox.phar to store local application statistics. This folder contains Sqlite database which should never be uploaded to your production environment. It stores debug info such as request log, extensive error tracing info and so on.

### vendor, composer.*

`vendor` folder contains 3rd party add-on code and is managed by composer.phar. You shouldn't change composer.json or composer.lock as those files are updated automatically by Agile Toolkit installer.

### configuration files

Agile Toolkit recognizes the following configuration files:

 - config-sandbox.php will contain automated settings for your Sandbox environment. This file is updated automatically by Agile Toolkit Sandbox.
 - config-default.php contains default settings for your application and will synchronized.
 - config.php can be created to to locally override default settings.
 - dependencies - used internally by Agile Toolkit Sandbox
