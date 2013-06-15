# Core > Configuration

## The Core Config Files

In line with our design goal of simplicity, Agile Toolkit uses a PHP array for storing configuration values. Here's how it works:

1. In the root directory of your app, you will find the files `config-default.php` and `config-distrib.php`

1. The file `config-distrib` is a template and is never loaded &ndash; copy it to a file named `config.php` in the same directory when you are installing application for the first time. Avoid adding `config.php` into your distribution as it may contain sensitive data (database access). Make sure you do not overwrite this file or you loose some of your settings.

1. If Agile Toolkit finds file `config-default.php` it will include it before `config.php`. You can put some shared configuration options there which can still be overridden inside `config.php`.

## Setting Config Values

Agile Toolkit makes extensive use of default values, so you need very few settings to configure a working application. This is just a sample &ndash; you should add settings as you require.

$config['dsn']='mysql://user:secret@localhost/my_db';

$config['sample']['setting'] = 20;

$config['billing']['realex']['merchantid'] = 'agile';
$config['billing']['realex']['account'] = 'internet';
$config['billing']['realex']['secret'] = 'xLmpVrtzYu';

$config['logger']['log_output'] = 'full';
$config['logger']['log_dir'] = 'logs';

## Using Config Values

To use a config value, call the `getConfig()` method in your application object.

    // Use '/' to separate array keys
    $secret = $api->getConfig('sample/setting'); 

    // Optionally, set a default value to use if no setting is found
    $secret = $this->api->getConfig('sample/setting', 10); 

## Default Configuration Settings:

Here are the most important defaults in the Toolkit Core:


 * timezone - sets `date_default_timezone_set` for you.
 * license - registration data for Agile Toolkit. See installation wizard.
 * session - override cookie settings for session, see `session_set_cookie_params`
 * auth/key - obsolete. Was used for SHA1 unique salt generation in Auth_Basic
 * dsn - connection string for SQL database
 * mongo/db - database for Controller_Data_Mongo
 
 * date/js - passed to date picker
 * locale/data - date formatting inside Grid
 * locale/datetime - date formatting inside Grid
 
 * js/versions/jquery - custom version of jQuery
 * js/versions/jqueryui - custom version of jQuery UI
 * js/jquery - set URL for jQuery if you wish to use CDN
 * js/jqueryui - set URL for jQuery if you wish to use CDN
 * logger/log_dir - folder where logs are written. By default `/var/log/atk4/REALM`
 * logger/log_output - how much info to write into log: false, true (for short) or `full` (default `"false"`)
 * logger/web_output - how much information to show to user, false, true (for short string) or `"full"` (default `"full"`)

 * url_prefix - prepends to page name in URLs, for example `index.php?page=` unless you have mod_rewrite.
 * url_postfix - what to add after page name, such as `.html` - will look like user is accessing HTML files in the url.

 * atk/base_path - normally ATK will look for ATK assets (such as css files, images) inside `./akt4`. If you are using mod-rewrite then sub-pages will break this behaviour. Set a proper prefix for `atk4` folder, such as `/myproj/atk4`.
 * atk/base_url - prefix URL for your application such as `http://mysite.com`
 * smlite/extensions - how do you want to end your temp ate files. Don't change (default is `.html`) this or you'll have to rename all your templates inside atk4/templates/shared
 * tmail/transport - by default TMail sends email with `mail()` method. You can set a different method here. If you are developing locally then using `debug` transport is a great idea - it will not send emails, but will simply dump email to the screen.
 * tmail/from - specifies the default sender for all emails sent from Agile Toolkit (TMail)


## Loading Additional Config Files

Sometimes it's handy to keep the config settings for a part of your app in an additional file. Read them in with the `readConfig()` method:

    $this->api->readConfig('config-additional.php');

