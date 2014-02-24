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
