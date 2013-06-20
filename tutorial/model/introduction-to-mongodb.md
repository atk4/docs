Introduction to MongoDB
====
MongoDB is currently the most popular NoSQL engine implementing document storage. If have been using Agile Toolkit with MySQL, I strongly encourage you to try MongoDB.

This document will help you get started quickly even if you never have used MongoDB before.

### Installing Mongo
Simply follow some on-line guide on how to get Mongo on your local computer, because that's where we will be developing.

 * [http://stackoverflow.com/questions/3772582/mongodb-and-mamp](http://stackoverflow.com/questions/3772582/mongodb-and-mamp)

You will need to have "PHP Driver" as well as MongoDB server running. For production use, please refer to MongoDB documentation.

Once that's all setup, you should be able to connect from your command-line:

    $ mongo
    MongoDB shell version: 2.4.4
    connecting to: test
    > 

and phpinfo(); should tell you some information about mongodb driver.


Creating a MongoDB based CRUD
----

Start by creating a new model:

    class Model_Test extends Mongo_Model {
        public $table='test';
        function init(){
            parent::init();
            
            $this->addField('name');
            $this->addField('gender')->enum(array('M','F'));
            $this->addField('over_18')->type('boolean');
        }
    }

Then add this to your page:

    $this->add('CRUD')->setModel('Test');

And finally in your config file:

    $config['mongo']['db']='mydb';
    
You should have a working CRUD! With Mongo you don't need to create table structure or adjust them, they will be adjusted on the fly. If you need one more field, simply declare it in the model and that's all.

Implementation Details
----
MongoDB Model support is implemented using the new Controller_Data, therefore if you look into the "[Mongo_Model](https://github.com/atk4/atk4/blob/master/lib/Mongo/Model.php)" class you'll find that it's pretty short. The majority of the logic lives inside [Controller_Data_Mongo](https://github.com/atk4/atk4/blob/master/lib/Controller/Data/Mongo.php).

This makes our Mongo implementation universal and flexible and sitting there along with other data controllers such as [Array](https://github.com/atk4/atk4/blob/master/lib/Controller/Data/Array.php), [MemCache](https://github.com/atk4/atk4/blob/master/lib/Controller/Data/Memcached.php) and [Session](https://github.com/atk4/atk4/blob/master/lib/Controller/Data/Session.php).

### Specifying collection
Collection in Mongo is same as 'table' in SQL. For compatibility you specify $table property in your model which is used to select a collection.

### Joins
Mongo does not support joins, therefore you can't use SQL-specific `join()` syntax.

### Traversing
Mongo has a full support for traversing in both directors:

    // In Model Book:
    $this->hasMany('Chapter');
    $this->hasOne('Author');

This works identical to SQL implementation, so you can use in your code:

    $author = $book->ref('author_id');
    
    $chapters = $book->ref('Chapter');
    
### Conditions
Mongo supports conditions and you can define them in the format you're used to:

    $model->addCondition('over_18',true);
    
This will affect new records you create as well as limit records which model can access. Mongo provides support for advanced conditions, therefore if you wish to use those:

    $model->addCondition('age',array('$gt'=>18, '$lt'=>65));

Or you can use more advanced conditions like this:

    $model->addCondition('$where',
       "function() { return this.name == 'Joe' || this.age == 50; }");

    $model->addCondition('search_index',new MongoRegex("/^$prefix/"));

    $email
        ->addCondition('$or',array(
            array('from_id'=>new MongoID($user->id)),
            array('to_id'=>new MongoID($user->id))
        ));


You should refer to [PHP](http://php.net/manual/en/mongocollection.find.php) and [MongoDB](http://docs.mongodb.org/manual/reference/operator/) documentation for more information.

### ID Field
While we recommend using `id` field in SQL, mongo uses `_id` field. If you want to write a portable code, you should rely on `$model->id` property instead. 

    $model['user_id']=$this->api->model->id;
    
### Field types

While SQL usually stores only string values in its fields (or something which can be expressed by a string, such as integers), Mongo can store practically any data-type in any field. Here is example:

    $model->addField('interests')
        ->defaultValue(array('fishing','reading'))
        ->system(true);

Because array is not supported by Form, and Grid (yet), I have set the `system` flag on this field to keep it from appearing in the UI. You can, however, access it easily.

    $model->load($id);
    $this->template->set('interests',join(',',$model['interests']));
    
Due to PHP limitation, you can't modify the array through some syntax:

    $model['interests'][]='cycling';
    
You would need to use a temporary variable for that. Perhaps that would be fixed in the future versions of PHP.

    $tmp = $model['interests'];
    $tmp[]='cycling';
    $model['interests']=$tmp;
    
You should also keep in mind, that reference fields (fields containing IDs) are using special objects of type MongoID. Fortunately Model will do some work for you and if you specify those IDs as a string (passed from GET) they will automatically be converted into MongoID when your model is saved.

MongoID object can be cast into string normally, so you can use it in URLs:

    $this->api->url('author/info',array('id'=>$book['author_id']]));
    

### Hooks and behaviors

Mongo Model contains all the same hooks you would expect model to have: `beforeSave`, `afterSave`, `beforeInsert`, `afterUpdate`, `beforeUpdate`, `afterDelete`, `beforeDelete`, `afterDelete`, etc. Obviously there are no query passed as inside SQL Model, but you still can modify model settings. Here is a interesting way how to index your collection:

    $this->addField('search_index')->system(true);
    $this->addHook('beforeSave',function($m){
        $m['search_index']=array_map('strtolower',array_map('trim'
            ,explode(' ',$m['name'].' '.$m['email'])));
    });
    $this->db()->ensureIndex('search_index');

### Accessing Collection Object

Mongo model makes it easy for you to access [MongoCollection class object](http://www.php.net/manual/en/class.mongocollection.php) - simply call $model->db().

This lets you access lots of other interesting operations as well as access MongoDB class itself similar as how it's [explained in PHP documentation](http://www.php.net/manual/en/mongocollection.aggregate.php):

    $ops = array(
        array(
            '$project' => array(
                "author" => 1,
                "tags"   => 1,
            )
        ),
        array('$unwind' => '$tags'),
        array(
            '$group' => array(
                "_id" => array("tags" => '$tags'),
                "authors" => array('$addToSet' => '$author'),
            ),
        ),
    );
    $result = $article_model->db()->aggregate($ops);

You can also access the MongoDB class [for things such as text query](http://docs.mongodb.org/manual/tutorial/search-for-text/):

    $results = $article_model->db()->db->command(array(
        'text'=>$article_model->table,
        'search'=>$search
    ));
    
### Using Cursors
When you are using some custom queries you might end up with a cursor and would want to do something useful with it. Remember that `Lister`, or `Grid` can operate with any object which implements `Iterator` interface, so chances are you could do this:

    $grid->addColumn('line','name');
    $grid->addColumn('line','age');
    $grid->setSource($cursor);
    
which will show your results nicely.


### Other features which simply "work"
Many other features of Agile Toolkit simply work with Mongo:

 - Paginator just works
 - Column sorting just works
 - Limits and setOrder
 - CRUD::addRef works
 - Model iterating works
 - Filestore works
 - Many other add-ons work.
 
If something what supposed to work doesn't - [please submit a bugreport](https://github.com/atk4/atk4/issues).
