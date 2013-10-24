## Handling form submission

Forms are always submitted to the same page where they were added on. This is default and recommended approach for any web software. By doing this, you can perform validations in the same place, in the same file reducing the risk of human error of not doing any logical validation before showing the form.

Additionally — form will be fully aware of the data it will be receiving, so instead of reading form data from `$_POST`, you can get the same data from the form object.

Form class is used for both rendering and submit data fetching. There are 4 steps to form cycle

1. Initialize form
2. Load default data
3. If form submitted, it's handled
4. Form is rendered

[Execution process in more details](tips/execution-process-in-more-details.md "Execution process in more details")

### Dynamically Submitted Forms
When form is added to the page, it's being a normal page with action and using `POST` for submitting. However form automatically initialise form widget. This widget (file ui.atk4_form.js) is based on jQuery UI Widget Factory. It does not provide any visual enhancement for the form, however is designed to improve submission as well as add some additional features to the form.

Form's submit event is redefined to use `$('#form').atk4_form('submitForm');` method instead. This method collect field values, encodes them and sends using AJAX request using `$.atk4.get()`.

[Difference between $.atk4.get() and $.ajax](tips/difference-between-atk4.get-and-ajax.md "Difference between $.atk4.get() and $.ajax")

If for some reason jQuery UI form enhancement component wasn't loaded, then form is being sent through POST like a normal form.

### Using isSubmitted function
Use of this function is really simple. It returns true if form was submitted. If you are new to forms in Agile Toolkit, you must remember that Form and Page will be initialized twice. First time when form was rendered, second time when it is submitted. Therefore `isSubmitted()` must be always placed after form is fully initialized.

Typical use would put `isSubmitted()` into the if block. This construction is used often, so you might want to place it into the snippet inside your IDE / Editor

    if($f->isSubmitted()){
        $f->js()->univ()->alert('Hello World')->execute();
    }
When JavaScript chain is built and `execute()`'d, it will only be functional when Form was submitted through AJAX. If you are concerned about non-AJAX form functionality, you should use:

    if($f->isSubmitted()){
        $p=$this->api->getDestinationURL('./success');
        $f->js()->univ()->location($p)->execute();
        $this->api->redirect($p);
    }

Using onSubmit
Both methods are essentially doing the same thing. `isSubmitted()` have been used in older versions of PHP, where closures were not easy to create.

    if($form->isSubmitted()){
        ...
    }
however with closures you can now create a function call-back like this:

    $form->onSubmit(function($form){
        ...
    });

One significant change though is that `onSubmit()` will capture some exceptions and will automatically display them on the form. Therefore if your model has this

    throw $this->exception('Wrong data','ForUser')->setField('name');

Then "name" field will be highlighted with this error.

### Redefining Form::submitted()
If you are defining your own form, you may want to re-define `submitted()` function. This approach offers no major benefits over isSubmitted approach, but is preserved for historical reasons.