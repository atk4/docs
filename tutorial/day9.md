# DAY 9: THE ADMIN SYSTEM

With the addition we made yesterday on Jobeet, the frontend application is now fully useable by job seekers and job posters. It's time to talk a bit about the backend application.

Thanks to User Interface componets of Agile Toolkit, creation of Administration System is especially enjoyable.

## Creating New Interface
----
Admin system will have a different entry point into the system. That's done for security purposes mainly. Let's quickly create essential files for an installation of Agile Toolkit

First, create subdirectory 'admin'. Then create .htaccess file:

    RewriteEngine on
    RewriteRule     .html$            index.php   [L]

Next create index.php file, which will re-use same installation of Agile Toolkit:

    include '../atk4/loader.php';     
    $api=new Admin('jobeet_admin','jui');
    $api->main();

Next, API class, lib/Admin.php:

    class Admin extends ApiFrontend {

        function init() {
            parent::init();

            $this->dbConnect();

            $main_lib = $this->addLocation('..', array(
                    'php' => 'lib'
                ));
            $this->addLocation('atk4-addons', array(
                    'php' => array(
                        'mvc',
                        'misc/lib',
                    ),
                ))
                ->setParent($main_lib);

            $this->add('jUI');
            $this->js()
                ->_load('atk4_univ')
            ;

            $this->add('BasicAuth')
                ->allow('admin', 'admin')
                ->check();

            $m = $this->add('Menu', null, 'Menu');
            $m->addMenuItem('jobs');
            $m->addMenuItem('filestore');
            $m->addMenuItem('logout');

            $this->initLayout();
        }
        function page_index($p) {
            $crud=$p->add('CRUD');
            $crud->setModel('Job');
            if($crud->grid)$crud->grid->add('Paginator');
        }
    }
In order to be able to re-use library files from our main project "lib" folder, we are using pathfinder's "addLocation" function.

Admin makes use of 'BasicAuth'. It is an authentication class which is based on a simple username/password pair defined through allow() function. To get into your admin system you should use username "admin" and password "admin". If you want to read user data from database, use SQLAuth.

Because we do not want to spend too much time on fine-tuning our admin rigth now, we are going to define pages through API class. This way we are not creating custom class for every page. This approach is suitable when you are in a hurry to write the code. It is always a good idea to re-factor page_XXXXX pages into their own classes, so when you have time, do that.

Our index page will contain CRUD (Create, Read, Update, Delete) component for managing jobs. By default it uses all the fields from the model.

Actually... in many situations that's all you need to do. However lets look on how we can squeeze more potential from Agile Toolkit

## No-generators policy
----
Agile Toolkit has a "no-generators" policy. The reason being that adding stuff in Agile Toolkit is so simple and trivial that producing code doesn't look so time-saving anymore. Also writing code can have many advantages and can be much more flexible.

## Simplicity
----
When working on real-life applications your clients will often become confused with complex logic in admin. That's why agile toolkit is using minimalist approach. Let's however put some enhancements on our CRUD

CRUD component is a light-weight addon view class which is implemented in slightly more than 50 lines of code. For it's functionality CRUD relies on Grid, Form and JavaScript features of Agile Toolkit. That's why when you enhance CRUD, you actually are enhancing those individual components. Take a closer look at last line fo page_jobs(). It verifies if grid is present (it will be present only when in read mode), and adds paginator on grid specifically. Let's enhance this to make few columns sortable.

    if($crud->grid){
        $crud->grid->add('Paginator');
        $crud->grid->getColumn('type')->makeSortable();
        $crud->grid->getColumn('location')->makeSortable();
    }

Next let's limit visible columns in grid. Third argument to setModel() defines list of fields specifically to Grid. Replace $crud->setModel() with the following:

    $crud->setModel('Job',null,array('id','type','company','position','location',
      'is_public','is_activated','expires_at','email'));

How about adding a quick-search? Like Paginator, Quick-Search is not included into basic Grid functionality but is added through a special Filter. Grid however has a wrapper:

    $crud->grid->addQuickSearch(array('company','position','location'));

Quicksearch only operates with fields which were included in the query (using "having" clause), therefore if you want to filter by "description field", you would also need to add it into query. Luckily it can be done very easy:

    $crud->grid->dq->field('description');

If we want to create anything with a number of entries, then we need to make grid items selectable. Agile Toolkit implements selection of the items by placing list of selected IDs into form field of your choice. Therefore you would need to add a form first, then use $grid->addSelectable();

    $action_form=$crud->add('Form',null,null,array('form_empty'));
    $ids=$action_form->addField('line','ids');
    $crud->grid->addSelectable($ids);
    $action_form->addSubmit('Delete Selected');
    $action_form->onSubmit(function($f) use ($crud){
        $ids=json_decode($f->get('ids'));
        $m=$crud->grid->getModel()->dsql(null,false)->where('id in',$ids)->delete();
        return $f->js(null,$crud->grid->js()->reload())
            ->univ()->successMessage('Success');
    });

You should be already familiar with how form submission works. The form we are adding here would perform the following every time it is submitted:

* get JSON data from 'ids' field.
* create instance of Job Model.
* we could have done add('Model_Job') but we are re-using model from grid to make our code more portable.
* model's dsql() function returns dynamic query. Second argument defines if we want table alias or not. For delete we don't.
* Query produced by dsql() would contain all the conditions pre-applied by the model. This greatly helps us not to delete anything what's not visible on the grid.
* where() adds additional condition and also provides escaping for $ids argument.
* delete() builds query for deletion and executes it.
* $crud->grid->js()->reload() produces JavaScript chain for reloading grid.
* $f->js() produces a chain for showing success message. It also embedds Grid reloading chain.
* JS code returned from onSubmit handler will be executed.

Finally to hide the field containing "ids", let's change from "line" to "hidden" field type.

## Adding Category Editor
----
How about adding jQuery UI Tabs and having jobs in first tab, while having Categories in 2nd? First, add the following two line at start of page_index() function:

    $tabs=$p->add('Tabs');
    $p=$tabs->addTab('Jobs');

You might say here, "Hey, variable $p is used already!". Not to worry. Once we added Tabs to the page, we don't need it anymore. When we are adding first tab we are re-using the same variable, so that we don't need to change the following code. At the end of page_index() funciton add the following line:

    $p=$tabs->addTab('Categories')->add('CRUD')->setModel('Category');

That adds a perfectly working grid on a second tab.

## Adding extend() method to Job
----
Before adding this method, let's plan ahead a little. Extend option should only be available to admin. We already have 2 models for jobs: Regular "Job" and "Job_Public". To keep things secure let's add a new class into admin/lib/Model/Job/Admin.php. This model will only be available in Administration system. Add the following content into your new model:

    class Model_Job_Admin extends Model_Job {
        function extend($ids=null){
            if($ids){
                foreach($ids as $id){
                    $this->tryLoad($id);
                    $this->extend();
                }
                return $this;
            }
            $this->set('expires_at',date('Y-m-d',
                    strtotime('1 month',strtotime(
                            $this->get('expires_at')))
                    ));
            $this->save();
            return $this;
        }           
    }                   

Our $model->extend() function can accept optional argument with list of $ids. If you want to add some validation check here, in case you make mistake and call method incorrectly.

Next, we need to change CRUD to use our new model and add a button to our grid which would call extend. I will leave changing argument for setModel to 'Job_Admin' to you. Then inside the if($crud->grid) block add:

    $crud->grid->addColumn('button','extend');
    if($_GET['extend']){
        $new_expires=$crud->grid->getModel()->tryLoad($_GET['extend'])
            ->extend()->get('expires_at');
        $new_expires=date($this->api->getConfig('locale/date','d/m/Y'),
            strtotime($new_expires));                               
                                                                                                
        $crud->grid->js(null,$crud->grid->js()->univ()->successMessage('Extended job #'.
            $_GET['extend'].' till '.$new_expires))->reload()->execute();  
    }       

This will add new column with type "Button" to the grid. Clicking button produces AJAX request with $_GET['extend'] equal to ID of clicked row. We are using that to load model data and call extend() function;

We are relying on chaining quite a lot here. Many functions such as loadData and extend() will return $this making it possible to call more functions on returned value. We are converting system date into proper format. Finally success message is displayed and grid is reloaded.

### Adding Extend as a multi-row action