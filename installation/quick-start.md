<!-- Status: Draft -->
Quick Start
====
The quickest way to install Agile Toolkit is by running this command from your terminal:

    curl -sS https://agiletoolkit.org/install | sh

You should execute this command from the empty folder where you would like to have you new Agile Toolkit project installed. Agile Toolkit installation relies on http://composer.org/ so you will need to have "git" and "php" working in he command-line.

If you are looking to install an existing project developed with Agile Toolkit, please refer to [Project Install](project-install.md) section.

Installer - New Web Application
----
Once you have Agile Toolkit downloaded, open the corresponding folder through your web server:

    http://domain.com/myproject/install.php
    
Installer will help you to configure your installation of Agile Toolkit in the following steps:

 * Capabilities check - installer will perform several tests on your platform to see how it is configured.
 * Configuration - on this step, with your help configuration for Agile Toolkit will be recorded and stored in config file.
 * First Page Snippet - Agile Toolkit helps you to build the first page of your application for you. 
 * Signing the license - officially registers your copy of Agile Toolkit by signing your own site certificate.
 * Further steps - how should you continue working with Agile Toolkit
 
Note: With the above example URL your project would be installed in `http://domain.com/myproject/public/`

Note: During this process installer will require write access to your web folder. In particular: `config.php`, `config-default.php`, `vendor/` and `page/mypage.php`. For best security practices, read the next chapter.

Single-page Installation (PHAR install)
----
Often you would want only a single page on Agile Toolkit or use only a selective features. In this scenario you should consider using Agile Toolkit PHAR archive.

    curl http://agiletoolkit.org/atk4.phar
    
This will create only a single file on your system, which you can place anywhere you need. Next you would need to include it and initialize appropriate application class. Below are two examples. First one will create a simple CRUD interface for you:

```
require'atk4.phar';

$api = new ApiWeb();
$model = $api->add('GenericModel');
$model->setTable('user');

$crud=$api->add('CRUD');
$crud->setModel($model);

$api->main();
```

If you are only interested in HTML rendering of a single component, use this: `$html = $crud->getHTML();`. Further integration with other frameworks and software and quirks is further discussed in the section [Integration](integration.md).

Manual Installation
----
Agile Toolkit resides in the main namespace along with your application. Standard installation relies on Composer to populate Agile Toolkit under vendor/atk4/atk4 folder. However you can get this folder directly from Github source, place it anywhere on your system. You would need to specify require `atk4/loader.php` or `atk4.phar` file inside your catch-all PHP file.

```
require '../atk4/loader.php';
$api=new MyApi();
$api->main();
```

Normally you would add any further modification into your definition of MyApi class. You can find out more under "Application" section of our documentation.

