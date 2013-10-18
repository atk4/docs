Create your own view in 3 steps:
---

Step 1: Create a view class and place it in libs\View folder.

A view is always extended from a view class. In Agiletoolkit, we always add prefix `View_` infront of the view name. It is a naming convention which is optional.

    class View_Freequote extends View {

Example Code:

    <?php

    class View_Freequote extends View {
    	function init() {
    		parent::init();
    		
	    }
	
      //Add the following function if you have a view template
	    function defaultTemplate() {  
		    return array('view/free-quote');
	    }
    }


Step 2: Add this view into your page. 

    $this->add("View_Freequote",null, "FREEQUOTE");
    
Step 3: In your page add the view `spot` (meaning the place where this view will be displayed)

    <?$FREEQUOTE?>
