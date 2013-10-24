## How Does a Form Work?
----

Form in Agile Toolkit will handle all aspects of the web form starting from form building, display, layout, submission, validation, database or model integration. In it's implementation, form relies on number of other object such as `"Fields"`, `"Buttons"`, `"Templates"`, etc. Form inherits ability to manipulate Templates and JavaScript from `AbstractView`.

When you add form, it will automatically add support for js_widget (jQuery UI based widget which ensures that Form's data is sent safely over AJAX). Optionally forms may contain several Fieds, Buttons. Form can also have a "Layout" which defines a custom-look for it's body. Form may be associated with database and may rely on `dsql()` object.

[What is under the hood?](tips/what-is-under-the-hood.md "What is under the hood?")

[List of default features supported by Forms](tips/list-of-default-features-supported-by-forms.md "List of default features supported by Forms")

[Additionally functionality](tips/additionally-functionality.md "Additionally functionality")

[How Forms in Agile Toolkit are different with Forms in other Frameworks?](tips/how-forms-in-agile-toolkit-are-different-with-forms-in-other-frameworks.md "How Forms in Agile Toolkit are different with Forms in other Frameworks?")

### Adding Form
    $p->add('Form');
Once you add the form as shown above, it will be automatically rendered by Agile Toolkit, it will have it's submit handler properly configured, it will perform necessary input data loading and validation. If you have multiple forms on the page, they will work independently without affecting each-other.

Agile Toolkit uses POST to submit all forms, which is enhanced through jQuery UI widget and is sent through an AJAX request. Form will also receive unique ID attribute.

Practically, use of Forms in Agile Toolkit require no set-up at all


## Sample form and form design philosophy
----
Most PHP frameworks this is as far as functionality goes. Agile Toolkit brings a lot of powerful ways to enhance forms, which are explained in further sections.

### Basic Form Example

Form is a fundamental component of any web application. Agile Toolkit greatly simplifies form creation.

    $form=$page->add('Form');
    $form->addField('line','name');
    $form->addField('line','surname');
    $form->addSubmit();
 
    if($form->isSubmitted()){
      $form->js()->univ()->alert('Thank you, '.
        $form->get('name').' '.$form->get('surname'))->execute();
    }

## Common questions answered about forms
----
[I want to change HTML/Layout of the form!](tips/i-want-to-change-html-Layout-of-the-form.md "I want to change HTML/Layout of the form!")

[What about file uploads?](tips/what-about-file-uploads.md "What about file uploads?")

[Is there way to re-order fields in the form, after they are defined?](tips/is-there-way-to-re-order-fields-in-the-form-after-they-are-defined.md "Is there way to re-order fields in the form, after they are defined?")