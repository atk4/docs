# DAY 4: THE CONTROLLER AND THE VIEW

Yesterday, we explored how Agile Toolkit simplifies database management and connect it to your first CRUD and by using relationship you saw how dropdown values were automatically populated from the right table.

Today, we are going to customize the basic job module we created yesterday. The job module already has all the code we need for Jobeet:

* A page to list all jobs
* A page to create a new job
* A page to update an existing job
* A page to delete a job

## The Agile MVC Architecture
----
If you are used to developing PHP websites without a framework, you probably use the one PHP file per HTML page paradigm. These PHP files probably contain the same kind of structure: initialization and global configuration, business logic related to the requested page, database records fetching, and finally HTML code that builds the page.

You may use a templating engine to separate the logic from the HTML. Perhaps you use a database abstraction layer to separate model interaction from business logic. But most of the time, you end up with a lot of code that is a nightmare to maintain. It's more and more difficult to make changes, especially because nobody except you understands how it is built and how it works.

As with every problem, there are nice solutions. For web development, the most common solution for organizing your code nowadays is the MVC design pattern. In short, the MVC design pattern defines a way to organize your code according to its nature. This pattern separates the code into three layers:

The Model layer defines the business logic (the database and models we created yesterday belong to this layer). You already know that Agile Toolkit stores models in the lib/Model/ directory. Model in Agile Toolkit is a class. Model examples are: User, Job, Category, Admin etc.

The View in Agile is also a class. If you looked at some other frameworks you might have thought it would be a HTML template. Nope. It's a class. However any View class is connected with a template - a chunk of HTML code which View uses to draw itself.

The Controller is a piece of code which binds Model with View. It teaches view how to operate with data and what kind of fields you have in your model. Controller is also a class.

## How MVC in Agile Toolkit is different?
----
Agile Toolkit is challenged with a difficult task - provide a flexible User Interface. Therefore a traditional approach to MVC does not work here, instead there are a number of simplification needed.

### Optional MVC
Traditionally in frameworks a developer needed to create all three pieces of M.V.C. In Agile Toolkit you can often use standard ones. If you look at the example you have (page/test.php), then you can see that we only have used standard View ("CRUD") and our own Model. The controller also got initialized in the process and Agile Toolkit picks the best crontroller for the job. You can access controller through $view->getController(); With this approach, there is much less code to develop.

### Everything is a class
The view is implemented as a class. It contains some UI logic on its own and can interract with the browser in certain ways. It is not necessarily complex, because views often inherit things and rely on other things. You would be surprised to find out that the CRUD View you used yesterday is implemented in under 50 lines of code. That's because it relies on other views such as Button, Grid, Form, Paginator etc

### Initialize then Render
A very major aspect of Agile Toolkit is separation of initialization and drawing. You might have added two CRUDs in your test.php init() method, but only later they produced any code. As a result, many things can happen with them inbetween - you can load data, add more fields, more buttons, more columns.

### Pages
Agile Toolkit introduces a concept of "page". Any class stored under "page" directory becomes a page. That means it can be requested through the browser. You as developer control and create all the pages. Page is used as a glue to put things together. Pages also control the flow and handle requests from the user.

## Component Design
----
Before we go into Design and HTML, there are some important things to consider.

First, if you have a closer look at the mockups, you will notice that much of each page looks the same. You already know that code duplication is bad, whether we are talking about HTML or PHP code. In fact, most of web application and websites will have common HTML code in them. Agile Toolkit comes with a flexible templating system.

Templates in Agile Toolkit contains a lot of useful things which PHP developers wouldn't know how to use and designers might be too lazy to add. If you have been following progress on the web, you would know that CSS can pretty much define the look of your page completely. If you do not agree, take a look at CSS Zend garden.

As a result rewriting HTML without thinking is a pretty lousy job. In fact the phase "cut into HTML" should sound more like... "cut into CSS". Agile Toolkit produces both HTML and CSS for your needs and it relies on the powerful jQuery UI CSS Framework and Themeroller.

### What not to do
So before we continue, let's agree on things we will NOT do.

* if you are not designer, don't bother changing any CSS or HTML manually
* do not replace HTML completely (even though it's possible), but we will ask designer to produce design for Agile Toolkit
* do not replace CSS completely, but only the part which defines the theme of our site
* do not replace component HTMLs without need

Doing any of the above will probably add you extra few weeks of work you could have avoided and save you from lots of possible errors on your site

### What if you want to do it anyway
You have been warned. If you still want to make your own skin you first need to decide if it will be based on jQuery UI or not. The best way to go with jQuery UI skins is to create template/jui/ directory. Copy file atk4/template/jui/shared.html into template/jui/. Open the file and start editing.

    <link rel="stylesheet" type="text/css" href="<?template?>css/agile.css<?/?>" />
    <link rel="stylesheet" type="text/css" href="<?template?>css/general.css<?/?>" />
    <link rel="stylesheet" type="text/css" href="<?template?>css/atk-icons.css<?/?>" />
    
Pay attention to the tag used here <?template?>. It is going to replace contents of the tag (css/general.css) with a properl URL to the file. When you are including more CSS files you need to always use this tag. This will allow you to easily deploy application on any domain and inside any directory. Another benefit you get is that Agile Toolkit will look for file inside templates/jui/css/general.css and if file is not found there atk4/templates/jui/css/general.css will be used instead. That technically allows you to change CSS files without going into atk4 directory and without changing the shared.html template.

The other thing you might have noticed is <?page?>. This generates a proper link to another page in your application. On Day1 we charged all pages to include .html at the end and with this tag you will keep all the link consistent.

### JavaScript
Agile Toolkit also handles JavaScript related tasks. Different views may choose to use certain JavaScript widgets which will be dynamically loaded. This is done in such a way to ensure that even AJAX content gets proper JavaScript treatment. If you are only learning Agile Toolkit, you should probably leave JavaScript alone for now.

### Being a PHP Developer
As a PHP developer you now have a very rare choice to focus on the PHP part of your project. Design certainly will be improved later by a skilled designer on your team and JavaScript enhancements will be added too, but not now. Agile Toolkit will try to enable you do everything that is needed without dipping your head into those areas.

## Adding Pages
----
Let's add a page for our current job listing. You will need to edit page/jobs.php file (which comes with atk4-example by default). You would need to get rid of everything in the file and put the following code there:

    class page_jobs extends Page {
        function page_index(){
            $p=$this;

            $jobs = $this->add('Grid');
            $jobs->setModel('Job',array('location','position','company'));
            $jobs->addQuickSearch(array('location','position'));
            $jobs->addButton('Post a Job');

            $jobs->addFormatter('company','link');
        }
        function defaultTemplate(){
            return array('page/jobs');
        }
    }
After open templates/default/page/jobs.html and change it to this:

    <h1>Jobeet</h1>
    <?$Content?>
Next we need to add the page which will show the details of the job. We will add it as a sub-page of page_jobs class. This can be done by creating function page_details(). When you have same page class treat multiple pages, then init() method is always executed. Method page_details will be executed for sub-page and method page_index() will be executed for the main page only.

Next, let's create a template for the details page's View. We are not redefining template for all of the page, just for a single view, which will contain job details, therefore we will save it as templates/default/view/job_details.html. Put the following code there:

    <h1><?$company?></h1>
    <h2><?$location?></h2>
    <h3>
        <?$position?>
        <small> - <?$type?></small>
    </h3> 
        
    <?has_logo?>
        <div class="logo">
            <a href="<?$logo_url?>">
                <img src="<?$logo?>" alt="<?$company?> logo" />
            </a>
        </div>
    <?/?>

    <div class="description">
        <?$description?>
    </div>
        
    <h4>How to apply?</h4>
    <p class="how_to_apply"><?$how_to_apply?></p>
 
    <div class="meta">
        <small>posted on <?$created_dts?></small>
    </div>

    <?$Buttons?>
You will also need to add a bit of PHP code to make this work with a model. Add this function to page/jobs.php

    function page_details(){
        $v=$this->add('View',null,null,array('view/job_details'));
        $m=$v->setModel('Job')->tryLoad($_GET['id']);
        $v->template->del('has_logo');
        $v->add('Button',null,'Buttons')->setLabel('Back')->js('click')->univ()->location(
            $this->api->url('..'));
        $v->add('Button',null,'Buttons')->setLabel('Edit');
    }

### See it working
Open http://localhost/jobeet/jobs.html in your browser

It should show you list of jobs from the database. Each of them should contain a link. Clicking a link will send you to the page jobs/details.html?id=123 which will show details of that particular job.

The views you have used on the pages are standard - Grid and View. You also coupled them with model through "setModel()". For the View on details page you specified a custom template through the fourth argument of the add() function. Further - you loaded data from the $_GET['id']. Finally two buttons were dynamically added to the details page to get back to the list or edit data.

## Setting page title
----
It would be awesome if we could have changed Title of the details page to contain job name. First we need to look into shared.html. It contains the following code:

    <title><?page_title?><?/?>Agile Toolkit</title>
Knowing that shared.html template is associated with the API, we can set it from anywhere in the application through $this->api->template>>set('page_title','Hello World'); Let's add this code to our details page.

        $this->api->template->trySet('page_title',
            sprintf('%s is looking for %s',$m->get('company'),$m->get('position')));

While here, we also removed "fullscreen" tag contents from the shared file, which will set screen width to 960 pixels. Last line here includes notification widget, which we will certainly make use of for status messages. Next open template/jui/page/jobs.html and add anywhere: <?seo_title?>Job Listings<?/?>

## Handling errors
----
You might think, what would happen if you try to access page with non-existent ID. This would generate exception "InstanceNotLoaded". Calling tryLoad() with non-existang ID does not produce error on it's own, however trying to get properties after that produces the error. You can either check this manually by calling $m->loaded() function after tryLoad(). Alternatively and preferably you should let Agile Toolkit handle this. If problem bubbles up, API catches it and depending on deployment setting will produce either an verbose error message or just a static screen.

## See you Tomorrow
----
Today, we have described some design patterns used by Agile Toolkit. Hopefully the project structure now makes more sense. We have played with the templates by manipulating the tags. We have also used view and grid to display the data the way we want it.

Tomorrow, we will learn more about the routing.