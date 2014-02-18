<!-- draft -->

Agile User Interface
====
Agile Toolkit is an Object-Oriented PHP UI Framework. That means:

 - Object Oriented: You operate with Objects capable of visualizing themselves.
 - PHP: You operate in the most popular web language on the planet.
 - UI: Agile Toolkit takes care of all aspects of user interface.
 - Framework: There are strict rules and well-organized foundation.
 - Toolkit: You have a collection of tools you can use right away.
 - Agile: Everything is super flexible and customizable.
 
If you are new to Agile Toolkit please refer to section  
 
 

abstraction of HTML/CSS User Interface. That means that PHP code you write will look great.




as you create PHP objects, they render themselves into a web application. 


Agile Toolkit offers you a fully-working out-of-the-box HTML/CSS User Interface. You can control the 





From all the PHP frameworks and toolkits who tried to create User Interface you will find Agile Toolkit to contain the perfect mix between "template customizability" and "content awareness". In Agile Toolkit you have a full control over every line of HTML produced. At the same time Agile Toolkit offer AJAX and JavaScript integration on object-level allowing you to reload objects on the page, assign JavaScript and AJAX events.





Create your own View in 3 steps:
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


Step 2: Add this view into your page. (example `index.php`)

    $this->add("View_Freequote",null, "FREEQUOTE");
    
Step 3: In your `html` template of the page add the view `spot` (meaning the place where this view will be displayed)

    <?$FREEQUOTE?>


See Links:
 
 - add() : http://agiletoolkit.org/dox/html/class_abstract_object.html#a166878abcde3d0e82826ed3110c37c87
