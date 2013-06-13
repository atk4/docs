# Core > Configuration

## The Core Config Files

In line with our design goal of simplicity, Agile Toolkit uses a PHP array for storing configuration values. Here's how it works:

    // TODO: can users add their own configurations to config-default,
    // or will they be overwritten when they upgrade?? It's a bit confusing...

1. In the root directory of your app, you will find the files `config-default.php` and `config-distrib.php`

1. The file `config-distrib` is a template and is never loaded &ndash; copy it to a file named `config.php` in the same directory

1. Early in the execution flow your API class will load the file `config-default.php` and add it's settings to the API `$config` array 

1. Then it will load `config.php`, so values from `config-default.php` will be overwritten by any values in `config.php` with the same array keys.

## Setting Config Values

Agile Toolkit makes extensive use of default values, so you need very few settings to configure a working application. This is just a sample &ndash; you should add settings as you require.

$config['dsn']='mysql://user:secret@localhost/my_db';

$config['sample']['setting']=20;

$config['billing']['realex']['merchantid']='merchant';
$config['billing']['realex']['account']='';
$config['billing']['realex']['secret']='secret';

$config['logger']['log_output'] = 'full';
$config['logger']['log_dir'] = 'logs';

## Using Config Values

To use a config value, call the `getConfig()` method in your API.

    // Use '/' to separate array keys
    $secret = $api->getConfig('sample/setting'); 

    // Set a default value to use if no setting is found
    $secret = $this->api->getConfig('sample/setting', 10); 

## Core Config Options

    // TODO: please list the important options and their default Values
    // The problem with all the default settings is that they're not easy
    // to find!

## Loading Additional Config Files

Sometimes it's handy to keep the config settings for a part of your app in an additional file. Read them in with the API `readConfig()` method:

    $this->api->readConfig('config-additional.php');
