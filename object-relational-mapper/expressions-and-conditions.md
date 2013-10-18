# Object Relational Manager (ORM) > Expressions and Conditions
----

## DEFINING EXPRESSIONS

Worderful SQL language permins the use of expressions in `SELECT` queries and Agile Toolkit has a way to use expressions as a regular fields. Those fields can be referred to as "calculated" fields.

Expressions are implemented as a class extended from a "Field" class, however they modify master DSQL in a different way.

    $model->addExpression('age')->set('year(now())-year(birth_date)');
Expressions can also be defined as a callback. The callback will be executed when the query is being built, only once.

    $author->addExpression('books_written')->set(function($model,$select){
        return $select->dsql()
            ->table('book')
            ->field($select->expr('count(*)'))
            ->where('author_id',$select->getField('id'));
    });
function set receives a callable argument. This callable will be called with a select DSQL query as an argument. We are using it to build a sub-query which performs select from table 'book'. The sub-select is supposed to execute count() expression but it's also important to have author_id linked with the "id" of the select query.

A handy short-cut is to use the fact that second argument to `where()` can be either a number or a field and storing referencing information in a separate function.

    class Model_Author extends Model_Table {
        function init(){
            parent::init();
    
            $this->addExpression('books_written')->set(function($model,$select)use($self){
                return $model->refBooks($select->getField('id'))
                ->dsql->field($select->expr('count(*)'));
            });
        }
        function refBooks($id=null){
            return $this->add('Model_Book')
                ->addCondition('author_id',$id?:$this->id);
        }
    }
In this example, we define a function which returns a model for listing all books of a currently loaded author. If numeric argument is specified, then books of that particular author will be returned inside the model. The expression, however, takes advantage of that model. Because expression is used in `SELECT` query it need to use field instead of a predefined numeric `ID` value.

By passing `getField()` as an $id argument it will properly insert a reference to a table and field of a master select. Model returned by a function is then only used to retrieve DSQL object and configure it to select only a statement.

As already mentioned, it's irrelevant at this point if "Book" is defined by a single table or multiple or if it has some additional conditions — those will be applied automatically. Models even handle situations where Book is joined with the same table as Author.

## CONDITIONS AND MASTER FIELDS

The key difference between DSQL and Model is that Model has a strictly defined set of fields. Fields can be of a few types such as regular SQL fields, calculated fields or fields from joined table. All three cases will have different format when in a query. Developer who uses Model_Table, however, has a luxury of ignoring field type and placing a condition just by using field name.

    class Model_Ojiisan extends Model_User {
      // Ojiisan = Old Man
      function init(){
        parent::init();
    
        $this->addField('gender')->enum(array('M','F'));
        $this->addExpression('age','year(now())-year(birthdate)');  

        $this->addCondition('gender','M');
        $this->addCondition('age','>',65);
    
      }
    }
Above code will approach queries for both fields differently, but the syntax is consistent. This allows for UI to ignore nature of the fields and generically add conditions as needed.

Setting conditions will modify master query and it will have effect on selects, updates and deletes. It will not have any effect on `insert()` and therefore model will allow you to insert a record which you won't be able to load with the same model afterwards.

### Master Fields
Master fields are similar to conditions, but model will also use a default value for when inserting records. `masterField()` does not support operation.

    class Model_Ojiisan extends Model_User {
      // Ojiisan = Old Man
      function init(){
        parent::init();

        $this->addField('gender')->enum(array('M','F'));
        $this->addExpression('age','year(now())-year(birthdate)');  

        $this->setMasterField('gender','M');
        $this->addCondition('age','>',65);
      }
    }

    $model=$page->add('Model_Ojiisan');
    $model->set('age',20);
    $model->save();
In the example above, model attempt to insert record with gender=M and age=20. `save()` method will attempt to load model after it is successfully saved, but the condition will prevent model from loading. This will cause exception and the transaction will be rolled back.

`setMasterField()` will also mark field as system to avoid accidental change. Be aware that `setMasterField()` should happen before model is binded with view, otherwise system field flag will have no effect:

// WRONG

    $page->add('CRUD')
      ->setModel('Book')
      ->setMasterField('published','Y');
      
// RIGHT

    $m=$page->add('Model_Book')
      ->setMasterField('published','Y');
    $page->add('CRUD')->setModel($m);

## Conditions on Calculated Fields
Let's assume that we have a model called "Model_Member" and we are looking to extend it into "Model_Member_Active". Active members are those whoose "expiry_date" is in the future.

Let's look at two ways how to achieve this.

### Adding Conditions on Expression
Expressions are treated like a normal fields by a model. You can define an expression, then add condition on it.

    class Model_Member_Active extends Model_Member {
      function init(){
        parent::init();
        $this->addExpression('is_member')->set('if(expiry_date>now(),"Y","N")');
        $this->addCondition('is_member','Y');
      }
    }

### Modifying master DSQL
The other option is to simply modify master DSQL and have the condition embedded into it.

    class Model_Member_Active extends Model_Member {
      function init(){
        parent::init();
        $this->dsql->where($this->dsql->expr('if(expiry_date>now())'));
      }
    }

## Conditions on relative fields or joined models
Suppose you want to list all books by female authors. You would already have a book model, but it does not contain field for author. The following code accomplishes the task:

    $book_by_f=$this->add('Model_Book');
    $book_by_f->join('author')->addField('gender')->enum(array('M','F'));
    $book_by_f->addCondition('gender','F');
If you wish to have a standalone model, then you need to copy this code inside a model's init:

    class Model_Book_ByF extends Model_Book {
      function init(){
        parent::init();
        $book_by_f=$this;
        $book_by_f->join('author')->addField('gender')->enum(array('M','F'));
        $book_by_f->addCondition('gender','F');
      }
    }
Note that adding to this model would also create a related author record. To prevent accidental adding and avoid accidental change of the gender in a form, you can do this:

    $book_by_f->getField('gender');
    $book_by_f->addHook('beforeInsert',function($t){throw $t->exception('Do not add records!');});