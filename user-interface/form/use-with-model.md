### Use with Model

In Agile Toolkit models are used to describe business entities. Controllers allow to build integration between Model and View, such as Form.

    class Model_Employee extends Model_Person {
        function defineFields(){
            parent::defineFields();

            $this->addField('name')
                ->mandatory(true);

            $this->addField('days_worked')
                ->system(true)
                ->datatype('int');

            $this->addField('salary')
                ->mandatory(true)
                ->datatype('money');

            $this->addField('money_owed')
                ->caption('Owed')
                ->calculated(true);
        }
    }

### Building form from Model
To use Model functionality you would need to use MVCForm instead of Form. Calling `setModel()` or `setController()` will initialize form by adding fields and binding it with the model.

    $form = $page->add('Form');
    $form->setModel('Employee');
    $form->addSubmit();

It's recommended to use `setModel()`, which will use default controller for integration between Form and Model.

Sometimes it is required to omit some fields from the form, or change field order. This can be done through 2nd argument to setModel.

    $f = $page->add('Form');
 
    // defining arary of fields to show and sequence order
    $f->setModel('Employee',array('name'));

### Using existing Model
Instead of specifying name of the model, you can create the model object and specify exstinging model as a first argument.

    $model = $page->add('Model_Employee');
    $model->addCondition('salary','<=',3000);
 
    $f=$page->add('Form');
 
    $f->setModel($model,array('name','salary'));
    $f->getElement('salary')->setFieldHint('Must not exceed 3000');
    $f->addSubmit();
 
    $f->onSubmit(function($f){
        try {
            $f->update()->js()->univ()->successMessage('Saved with id='.$f->model->id)->execute();
        }catch(Exception $e){
            $f->js()->univ()->alert('Failed to add record')->execute();
        }
    });