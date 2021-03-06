# THE CONFIGURATION FILE

Agile Toolkit uses a PHP array for defining configuration. The file config-default.php and config.php then add more properties into the $config array variable, which is copied into the API during file inclusion. Below is a sample of a config.php file.

Like many other things, configuration options are optional in config.php. This is just a sample, and you should add lines as you require.

    $config['dsn']='mysql://user:secret@localhost/db';

    $config['sample']['setting']=123;

    $config['billing']['realex']['merchantid']='merchant';
    $config['billing']['realex']['account']='';
    $config['billing']['realex']['secret']='secret';

    $config['logger']['log_output'] = 'full';
    $config['logger']['log_dir'] = 'logs';

The method `api->getConfig('sample/setting',400);` is used to to retrieve config settings. The second argument to this function is a default value, and it's used extensively throughout Agile Toolkit add-ons and components.

## Loading Additional Config Files
By default Agile Toolkit will read the config.php and config-defaults.php files. The settings in config.php will override the settings in config-defaults.php. The files are loaded by the Api using the `readConfig()` method. You can, therefore, load configurations from additional files yourself, using the same method.

    $this->api->readConfig('config-extra.php');

The file config-distrib.php is never loaded. It is a "template" that you can use to make your own copy of config.php. The Agile Toolkit Installation Guide will typically suggest that you copy "config-distrib.php" and rename it to "config.php". You can then edit its contents. When you upgrade your Agile Toolkit installation, the upgrade will not write over your customised config.php file.