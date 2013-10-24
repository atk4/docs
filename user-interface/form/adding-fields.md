### Adding Fields
[Model-integration (MVC)](tips/model-integration.md "Model-integration (MVC)")

    $f=$p->add('Form');
    $f->addField('line','name');
Form class defines a method `addField()`, which is used to create Form_Field objects. Typically this field object will contain markup for label, error and hint. Objects returned by addField() can be further interacted with. At least 2 arguments must be passed to addField first being a field type and second is field name

Label can be passed optionally as 3rd argument. If it's omitted then name is slightly converted to make a decent label (first_name is converted into 'First Name')

When looking at examples, the variable `$f` is often used to reference forms. We suggest to keep variable `$f` to always reference a form. If you need to build cross-linking between multiple form, use object properties instead.

### Adding Buttons
    $f=$p->add('Form');
    $f->addField('line','name');
    $f->addSubmit('Greet Me');
Form defines 2 methods — `addSubmit()` and `addButton()`. Both are relying on 'Button' class, so technically you can add button elsewhere and instruct it to submit your form

### Form Elements

There are many field types you can use with form. In addition you can add your own field types or use add-on which may provide additional types.

**STANDARD FIELDS**

line — single line entry (input type=text)
    
    $form->addField('Line','line');
    
text — multi-line text entry (textarea)
    
    $form->addField('Text','text');
    
password — show masked text (password)
    
    $form->addField('Password','password');
    
hidden — hidden field (label will still be visible)
    
    $form->addField('Hidden','hidden');
    
search — search field (input type=search, non-standard)
    
    $form->addField('Search','Search');
    
time — formats time (input type=text)
    
date — formats date (input type=text)
    
money — formats money (input type=text)
    
checkbox — single checkbox

    $form->addField('Checkbox','checkbox');
    
**Further fields are based on ValueList**

dropdown — allows to select one value from list (textarea)

    $form->addField('Dropdown','dropdown');
    
checkboxlist — multi-line text entry (textarea)

    $form->addField('CheckboxList','checkboxlist');
    
radio — multi-line text entry (textarea)

    $form->addField('Radio','radio');

**More complicated fields**

 date — multi-line text entry (textarea)

    $form->addField('DatePicker','date');
    
DateSelector — multi-line text entry (textarea)

Slider — multi-line text entry (textarea)

    $form->addField('Slider','Slider');
    
Autocomplete — multi-line text entry (textarea)

Upload — multi-line text entry (textarea)

    $form->addField('upload','upload');
    
Grouped — multi-line text entry (textarea)