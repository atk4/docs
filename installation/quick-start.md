# Quick Start
----

The quickest way to install Agile Toolkit is by opening http://agiletoolkit.org/ and clicking on the download link. You will download a ZIP bundle containing the following:

 * Framework inside atk/ folder
 * Some demo applications
 * Few useful add-ons in folder atk4-addons
 * Sample application in other folders 

## Installer - New Web Application

Once you have Agile Toolkit downloaded, open the corresponding folder through your web server:

    http://domain.com/myproject/install.php

Installer will help you to configure your installation of Agile Toolkit in the following steps:

 * Register your Agile Toolkit with appropriate license (free or paid)
 * Download some recommended add-ons
 * Set-up database if you wish to use some examples
 * Save configuration into `config.php` file.
 * Capabilities check - installer will perform several tests on your platform to see how it is configured.
 * Build the first page of your application for you. 
 * Further steps - how should you continue working with Agile Toolkit
 
*Note: With the above example URL your project would be installed in `http://domain.com/myproject/public/`*

*Note: During this process installer will require write access to your web folder. In particular: `config.php`, `config-default.php`, `vendor/` and `page/mypage.php`. For best security practices, read the next chapter.*

### Securing your install
Your Agile Toolkit installation contains `public` folder. If you are concerned about exposing parts of your application on web-root you should change your web-root to point inside this `public` folder.

