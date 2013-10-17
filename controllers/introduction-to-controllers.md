#VIEW'S DEFAULT CONTROLLER

AbstractView property "default_controller" will specify a default controller which will be used for field population. If the property is "null" then fields are not populated. Typically the View will specify this value in it's definition. For example, 'Form' uses 'Controller_MVCForm'.

If the specified controller has method setActualFields and the list of fields were present as second argument, then that method is executed.

View uses setController to initialize default controller primarily due to developer convienence.

## setController
This method is designed to initialize controllers with any objects in your system. While Model primarily contains only field and business logic and would not contain any display-related logic, controllers are permitted to be much more diverse in what they are doing. Below are just some application types for the controllers:

### ADDING FIELDS
Controller may add additional fields to a model. Using this technique allows you to create standard audit fields:

    class Controller_ModelAudit extends AbstractController {
        function init(){
            parent::init();
            if(!$this->owner instanceof Model)
              throw $this->exception('Use with Model only');
    
            $this->owner->addField('created_dts');
            $this->owner->addHook('beforeSave',function($m){
                $m->set('created_dts',date('Y-m-d H:i:s'));
            });
        }
    }

### ALTERING OBJECT BEHAVIOUR
Objects do not have control on how exactly fields are being added into it. To re-order child objects, use "Controller_Ordering". There are many other controllers to look out for.

### SEE ALSO
Controller_MVCForm

Controller_MVCGrid