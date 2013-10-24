### Re-using forms

This chapter focuses on a single field and what can you do with it by extending Form_Field class or by relying on capabilities of Agile Toolkit and enhancing existing one.

    class GreetingForm extends Form {
        function init(){
            parent::init();
            $this->addField('line','name')->validateNotNull();
            $this->addSubmit('Greet');
            if($this->isSubmitted()){
                $this->js()->univ()->alert('Hello, '.$this->get('name'))->execute();
            }
        }
    }

### Accessing existing form fields, changing label
By default label is taken form field name, although you can specify it as a 3rd argument to `addField()` method. You can change label of existing field by using `setCaption()` method

    $f=$p->add('GreetingForm');
    $f->getElement('name')
        ->setCaption('Your Name');
    
### Customize Field Attributes
You can specify any additional attribute to a HTML element which is responsible for drawing field input.

    $f=$p->add('GreetingForm');
    $f->getElement('name')
        ->setAttr('maxlength',4);
    
### Disable field
Disabling field by calling `disable()` is preferred over setting the attribute "disabled" for 2 reasons: 1) this ensures that field data will not be saved, even if it's present in POST, 2) some fields may contain multiple input elements and will want to re-define this method.

Disabled fields do not handle JavaScript events

    $f=$p->add('GreetingForm');
    $f->getElement('name')
        ->disable()
        ->js('click')->univ()->alert('clicked');
    $f->addButton('Enable')->js('click',
        $f->getElement('name')->js()->removeAttr('disabled')
        );
        
### Disabling Enter
Formely a method in Form, this is now implemented as JavaScript enhancement.

    $f=$p->add('GreetingForm');
    $f->addField('line','Surname')
        ->js(true)->univ()->disableEnter();
    
### Working with field template
Like any other object in Agile Toolkit, you can change template text or insert objects into template.

    $f=$p->add('GreetingForm');
    $f->getElement('name')->template
        ->set('before_field','€&nbsp;');

    $icon=$f->getElement('name')
        ->add('Icon',null,'after_field')
        ->set('basic-check')
        ->setStyle('margin-left','10px');

    $f->add('Hint',null,'hint')
        ->set('Hint placeholder');
    $f->add('Text',null,'form_buttons')
        ->set('button placeholder');
    $f->add('H4')
        ->set('Text between fields');

    $f->addField('line','surname');

### Adding Hints
Although hint is also added by using a template, there is a special function for it.

    $f=$p->add('GreetingForm');
    $f->getElement('name')
        ->setFieldHint('Full name');
    
### Default Value and Reloading Fields
`set()` will change default value. Calling `get()` after `isSubmitted()` will properly prioritize submitted value. This example also show-cases field reloading.

    $f=$p->add('GreetingForm');
    $f->getElement('name')
        ->set(rand(10,99))

        ->add('Icon',null,'after_field')
        ->set('basic-check')
        ->setStyle('margin-left','10px')

        ->js('click',
            $f->js()->atk4_form('reloadField','name')
        );
            
### Detect which button was clicked
This example also show-cases field reloading

    $f=$p->add('Form');
    $f->addField('line','name');

    $f->addButton('One')->js('click',
            $f->js()->atk4_form('submitForm','one')
            );
    $f->addButton('Two')->js('click',
            $f->js()->atk4_form('submitForm','two')
        );

    if($f->isSubmitted()){
        if($f->isClicked('one'))
            $f->js()->univ()->alert('One is clicked')
                ->execute();

        if($f->isClicked('two'))
            $f->js()->univ()->alert('Two is clicked')
                ->execute();

            $f->js()->univ()->alert('Enter is used')
                ->execute();
    }

    // TODO: fix bug

### Standard Separator
Separator will close and reopen `<fieldset>` tag. This can be used to separate fields visually, but is also used for splitting fields into multiple columns where CSS supports it

    $f=$p->add('GreetingForm');
    $f->addSeparator();
    $f->addField('line','Surname');

### Preventing data loss
While some browsers already look for un-saved form data and will warn you when you attempt to close tab, there are no technique to do same thing through AJAX. Agile Toolkit automatically checks for any un-saved fields when AJAX attempts to destroy it and asks user to confirm it.

    $f=$p->add('GreetingForm');
    $f->getElement('name')->set(rand(10,90));
    $f->addButton('Refresh')->js('click',
            $f->js()->reload());
        
### Hide field line with JavaScript
You might want to hide the whole line with javascript

    $f=$p->add('GreetingForm');
    $f->getElement('name')
        ->js(true)->closest('dl')->hide();

    $f->addButton('Toggle')->js('click',
        $f->getElement('name')->js()
            ->closest('dl')->toggle());
        
### Cross-submit
While you keep the form object, you can bind it to any other object's event

    $f=$p->add('GreetingForm');

    $p->add('LoremIpsum')->setLength(2,15);

    $p->add('Button')->set('Submit that other form')
        ->js('click',$f->js()->submit());
    
### Home-made slider implementation
While you keep the form object, you can bind it to any other object's event

    $f=$p->add('GreetingForm');
    $field=$f->getElement('name')
        ->set(rand(10,100));

    $p->add('HtmlElement')->set('')
        ->addStyle('margin-top','10px')
        ->js(true)->slider(array(
            'value'=>$field->js()->val(),
            'change'=>$field->js()->_enclose()->val(
                $field->js()->_selectorThis()->slider('value')
            )
        ));
    
## Creating Field Class
Creating your own field class might still be necessary sometimes.

Home-made slider implementation
While you keep the form object, you can bind it to any other object's event.

    $f=$p->add('GreetingForm');
    $f->addField('DatePicker','date');

    class Form_Field_DatePicker extends Form_Field {
        public $options=array();
        function getInput($attr=array()){
            // $this->value contains date in MySQL format
            // we need it in locale format

            $this->js(true)->datepicker(array_merge(array(
                        'duration'=>0,
                        'showOn'=>'button',
                        'buttonImage'=>$this->api->locateURL('images','calendar.gif'),
                        'buttonImageOnly'=> true,
                        'changeMonth'=>true,
                        'changeYear'=>true,
                        'dateFormat'=>$this->api->getConfig('locale/date_js','dd/mm/yy')
                        ),$this->options));

            return parent::getInput(array_merge(
                array(
                    'value'=>$this->value?(date($this->api->getConfig('locale/date','d/m/Y'),strtotime($this->value))):'',
                ),$attr
            ));
        }
        function set($value){
            // value can be valid date format, as in config['locale']['date']
            if(!$value)return parent::set($value);
            list($d,$m,$y)=explode('/',$value);
            if($y)$value=join('/',array($m,$d,$y));
            elseif($m)$value=join('/',array($m,$d));
            $value=date('Y-m-d',strtotime($value));
            return parent::set($value);
        }
        function get(){
            $value=parent::get();
            // date cannot be empty string
        if($value=='')return null;
            return $value;
        }
    }

    class GreetingForm extends Form {
        function init(){
            parent::init();
            $this->addField('line','name')->validateNotNull();
            $this->addSubmit('Greet');
            if($this->isSubmitted()){
                $this->js()->univ()->alert('Hello, '.$this->get('name'))->execute();
            }
        }
    }