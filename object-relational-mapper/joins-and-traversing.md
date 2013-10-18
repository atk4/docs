# Object Relational Manager (ORM) >Joins and Traversing

## JOINING MODEL TABLES
One of the greatest benefits of Models in Agile Toolkit is the ability to work with multiple tables. Let's suppose that objects in your database have respective link in the "permission" table:

`user.permission_id = permission.id;`

Every time you add new user, the record should be added in the permission table. Also fields from the permission table must become fields of the "User" model. `join()` is answer to those requirements.

    class Model_User extends Model_Table {
        public $table='user';
        function init(){
            parent::init();

            $this->addField('name');
    
            $perm = $this->join('permission');
            $perm->addField('is_admin')->type('boolean');
            $perm->addField('is_writer')->type('boolean');
            $perm->addField('is_poster')->type('boolean');
        }
    }
Notice that `addField()` is called using the $perm instead of $this which instructs the model that fields are actually originate in a different table. Apart form using addField you can also use `hasOne()` / `hasMany()` to define relations based on the joined table.

### Different ways to join
The syntax of `join()` is very similar to `dsql()->join()` and can be used to create all sorts of joins. The above example demonstrates a most common join: user.permission_id = permission.id. If you are willing to build a reverse join you can use second argument:

    $this->join('permission','perm');
    // user.perm=permission.id

    $this->join('permission.user_id');
    // user.id=permission.user_id

    $this->join('permission.code','code');
    // user.code=permission.code

    $this->join('permission.id','id');
    // user.id=permission.id

### Chained joins
Sometimes you want to join table through the table which is already joined. The syntax is very intuitive:

    $perm = $this->join('permission');
    $res = $perm->join('resource');
With this you can now even define a field in "resource" and then use that field inside a condition. This allows you to fully use flexibility of SQL without overheads ORM systems usually introduce.

### Insert, Load, Modify
Another significant part of models with joins is that records are inserted in both tables when you create new model entry. If you use the user..permission..resource model described above and create a new record, then record is first added into "resource" table, then "permission" table record is added with a proper reference to "resource". Finally record in "user" table is created pointing to the "permission" table through permission_id field.

If a reverse relation is used (by supplying 2nd argument to join) then insertion happens in reverse order — first adding a user and then adding into permission table.

Model also properly handles loading and updates of the model.