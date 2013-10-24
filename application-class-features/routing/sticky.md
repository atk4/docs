# Sticky GET Arguments

Suppose you are building a widget, which needs to query itself for some additional data through AJAX requests. This technique is used in auto-complete fields, which query PHP server for list of matching entries.

If you place such a widget on your page, widget needs to know which URL to use. This is a standard practice in Agile Toolkit:

    if(isset($_GET['query'])) {
        return json_encode($model->getRows());
    }
    
    $data_url = $this->api->url(null, array('query'=>'?'));
    
Relative URL feature is perfect here as it will provide widget with a link to a current page and can also pass some arguments. Next you want to put this widget on the following page:

    /admin/user/edit?id=123
    
As you probably noticed, the edit page requires ID argument to be present and without it - it will probably fail. Widget MUST know that ID argument is expected and include it in the URL.

While other frameworks solve this problem by asking you to always specify URL for every widget, Agile Toolkit implements StickyGET. By placing the following at the top of the page, you ensure that all further calls to url() will also include current value of ID.

    $this->api->stickyGET('id');
    
This method also returns the current value of 'id' so you can use it in combination with load method:

    $model = $this->add('Model_User')
        ->load($this->api->stickyGET('id'));
        
It's important to note, that any URL generated before call to stickyGET will NOT contain the ID value:


    $url1 = $this->api->url();
    $this->api->stickyGET('id');
    $url2 = $this->api->url();
    
    echo $url1;  //   /admin/user/edit
    echo $url2;  //   /admin/user/edit?id=123
    
Many widgets rely on stickyGET. One of the most common uses is inside VirtualPage.

    $grid = $this->add('Grid');
    $user = $grid->setModel('User');
    
    $grid_reload = $grid->js()->reload();
    
    $grid->add('VirtualPage')->addColumn('Process')
        ->set(function($p) use ($user, $grid_reload){
            $output = $user->load($p->id)->process();
            
            $p->add('View')->set($output);  // show user output
            $p->add('Button')->js('click',$grid_reload)
                ->univ()->closeDialog()->execute();
        });
    
The example above might be a bit fought, so I'll walk you through it. First two lines create a grid and set model. Pretty basic stuff.

Next, calling js()->reload() returns a jQuery chain which refresh the Grid when executed. We don't need to reload grid just yet, so we put it into a variable and don't pass js() any arguments.

VirtualPage, when added into Grid allows you to use method addColumn. This adds a new column to a grid resulting in a pop-up. When pop-up is opened, it's content will be filled by a virtual page - in particular $p view from the closure.

VirtualPage will send AJAX request to itself with an additional STICKY argument which will be affecting all URLs generated within the closure. This ensures that any interactive object you would have inside the VirtualPage would be able to reach itself.

TODO: help me explain this code better!!!

