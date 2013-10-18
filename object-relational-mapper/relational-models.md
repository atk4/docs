# Object Relational Manager (ORM) > Relational Models
----
The syntax of defining Relational Models is very similar to a regular models. There are however few differences:

 * You should inherit from Model_Table
 * You do not need to use `setSource()`
 * You must specify `$table` property for each model, which is primary table
 
In addition, you have more methods to use and define fields.
Defining and Traversing References between Models

    class Model_Author extends Model_Table {
        public $table='author';
        function init(){
            parent::init();
            $this->hasMany('Book');
            $this->hasOne('Country');

            // field definitions
        }
    }
A further use of the model can use function `ref()` to traverse towards other records. You must, however, load author record before traversing.

    $author = $this->add('Model_Author');
    $author->load($id);
    $c_code = $author->ref('country_id')->get('Code');

    $this->add('Grid')->setModel($author->ref('Book'));

When traversing relations defined as "hasOne" the record of related model is automatically loaded. You should use the name of the field as an argument to `ref()` function.

When traversing relations defined as `hasMany()` the returned model will not be preloaded with any records. It will however have a condition added to make sure any new records you are going to add with that model will belong to author identified by $id.

## Building model from multiple tables
Let's assume that to store additional information about the author a new table "author_details" is created. This table contains "author_id". While in theory one author may have multiple records in author_details our logic dictates that it's one-on-one relationship. "author_id" is also defined as a primary field.

While we could have used references, it wouldn't let us join 2 tables seamlessly. To help out `Model_Table` has a `join()` method.

    // init of Model_Author
    $details = $this->join('author_details.author_id');

    $details->addField('bio');
    $details->addField('note')->as('details_note');
    
### Default Fields
Agile Toolkit uses some assumptions about the relations between fields. Table 'author' will have 'country_id' which is constructed by looking into 'Country' model's `$table` property and appending `'_id'`. That field is then loaded based on the `"id"` field of Author. If your id field is called differently, you can change id through a property `"$id_field"`.

Sometimes you need to use a different field name when referencing. Then you need to use 2nd argument to `"hasOne('Country','birth_country_id')"`.

## Loading and Saving data
**$model->load($id);**

Will cause exception if record is not found or null is specified. Much safer especially if you load from `$_GET[‘id’];`.

Also supports expressions in a limited way: `$model->load( $model->dsql->expr(‘getMyID()’));`.

**$model->tryLoad($id)**

Will NOT cause exception if record of particular `$id` is missing. Similar to `loadData()`. It is however WILL cause exception if `$id` is null. (`loadData` wouldn’t)

**$model->loadAny()**

This will attempt to load the first record. This is great function if you are sure that only one record exists, such as

    $user->ref(‘ContactInfo’)->loadAny();
It will throw exception if no record exist.

**$model->tryLoadAny()**

Combines both qualities. Attempts to load first possible record, but if not found will do nothing.

**$model->loadBy($field,$condition,$value);**

This have received a significant flexibility in the arguments. Actually it relies on `addCondition()`, but it’s still powerful.

    loadBy($model->dsql->expr(‘rand() > 0.2’) );
    loadBy($model->dsql->expr(‘rand()’), ‘>‘, $_GET[‘id’] ); // safe against injeciotn
    loadBy(‘calc_field’, 200); // using calculated field automatically switches to “having” clause.
   
most importantly, the condition specified in the arguments will NOT be applied on the model, so you can perform multiple attempts to load data.

**$model->tryLoadBy($field,$condition,$value);**

Identical to loadBy but will not complain about missing data.

### Saving data
**$model->save();**

method which will cause exception if it couldn't subsequently load the record. For example:

    $model->addCondition(‘gender’,’M’);
    $model->loadAny();
    $model->set(‘gender’,’F’);
    $model->save();            // causes exception, because newly saved record can’t be loaded.
    // If database supports roll-backs it will also roll-back.

**$model->saveAs($model);**

Allows to cast one model into another.

    $boy=$this->add(‘Model_Person’)->addCondition(‘gender’,’M’);
    $girl=$this->add(‘Model_Person’)->addCondition(‘gender’,’F’);

    $boy->loadAny();
    $boy->saveAs($girl);
// As a result of this, `$boy->loaded()` will now be false. `$girl->loaded()` however will point to a new record.

**$model->saveAndUnload();**

This will save model into the database but will not load data back. The model `loaded()` will return false. Slightly better if you need performance.

**$model->saveLater();**

Will not save right away, but will save when model is being destroyed by Garbage Collector;

    function test(){
      $boy=$this->add(‘Model_Boy’);
      $boy->loadAny()->saveLater();
      $boy->set(’gender’,’F’);
    }
When the function exist, the reference to $boy is lost and model is destroyed. At this point it will be saved back into the database. No further loading is done.

## Using non-ID fields for loading
There are different ways how you can load model. For example calling `load()` without arguments will load the first possible match from the available data.

**loadBy($field,[$cond],$value)**

This function will load model through a non-ID field. The second argument is optional and if defined it can specify the relationship, such as:

    $model->loadBy('age','>','25');
You may also use a DSQL expression as a first argument and omit condition and values alltogether.

    $model->loadBy($model->dsql->expr('expire_dts>now()'));
If record is not found, exception is raised. To avoid exception use `tryLoadBy`

*NOTE: It's always better to use try methods than catching exception. When exception is raised it records backtrack which is quite time consuming.*

**getBy($field,[$cond],$value)**

This similar syntax will return array of hashes produced the model's select.

    $data = $model->getBy($model->dsql->expr('expire_dts>now()'));
    echo json_encode($data);
calling `getBy()` will not affect the currently loaded record.

**getRows(array)**
This is similar to getBy but it allows to define which fields you are willing to retrieve:

    $data = $model->getRows(array('id','name'))
    echo json_encode($data);

## Iterating Through Entities
Model supports agregation, which means you can use it like this:

    foreach($this->add('Model_Book') as $book){
        $book['sale']++;
        $book->save();
    }

## Debugging
Model has a method `debug()` which will turn on debugging mode on the queries it produces and executes. This relies on [DSQL debug()](non-relational-models/dsql.md "DSQL").

When iterating, the `$book` will always refer to the same model, but it will have appropriate record loaded.

## Deleting Entities
Model attempts to prevent accidental data loss and therefore `$model->delete()` will only remove data from the database if you either specify `"id"` or have a record loaded.