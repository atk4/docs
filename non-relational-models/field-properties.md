# Non-Relational Models > DEFINING FIELD META-INFORMATION
----

Once `addField()` is called the field will be listed as one of the model's elements. You can access existing field or even destroy it through `$model->getElement('field');` Sometimes when you extend model, you would want to change certain attributes of a field.

    class Model_Admin extends Model_User {
        function init(){
            parent::init();

            $this->getField('is_admin')
                ->system(true)
                ->defaultValue(true);    
    
            $this->getField('email')
                ->destroy();

            $this->addField('privileges');
        }
    }
When you will be using Model_Admin now, field configuration will be different. Email field will be ignored, but a new field "privileges" will be used. Admin's model does not specify a different value for $table property, therefore same table is going to be used.

Hierarchic models are even more useful when used with Model_Table's conditions.

Calling `destroy()` on an object will remove it from it's owner's elements array. If model is not referenced in any other way, Garbage Collector will get rid of it.

If you want for a model to represent only one specific record, you can load it's data by default, such as by calling `load()` inside `init()`. It will, however, not prevent model object from un-loading that record and loading other record. Calling `load()` without arguments will load first possible record.

## List of meta-data
| SETTER / GETTER                | DEFAULT                                      | EXPLANATION |
|--------------------------------|----------------------------------------------|-------------|
| `type()`, (obsolete: `datatype()`) | "string"                                     |Field type. In theory can be anything, but "int", "boolean" and "text" some special treatment. |
| `caption()`                      | ucwords(str_replace('_', ' ', $short_name)); | Field type. In theory can be anything, but "int", "boolean" and "text" some special treatment. |
| `group()`                        | null                                         | Defines which group field belongs to. Specifying string to `setActualFields()`, will display only field from that group. |
|`readonly()`                      | false                                        | Readonly field will not be saved or show inside forms by default. If explicitly requested, it will show as disabled field. |
| `mandatory()`                    | false                                        | Field must be set before saving. Forms will mark respective field with asterisk, if displayed. |
| `editable()`                     | false                                        | Similar to `readonly()` but you can manually `set()` this field and it will be saved into database if changed. It still won't be editable on form. |
| `allowHTML()`                    | false                                        | By default, all fields will be stripped from HTML tags as additional security measure when loaded from Form. allowHTML==true will not strip tags. |
| `display()`                      | null                                         | Override how the filed is displayed inside views. By default Controller_MVCForm and Controller_MVCGrid will attempt to match column/form field type based on model field type (`type()`). Accepts either a string or array(`'form'=>'upload','grid'=>'text'`). |
| `system()`                       | false                                        | System fields are always loaded even if grid/form does not require them. They are not shown by default on the views, but will display if specifically requested. |
| `hidden()`                       | not sure...                                  | not sure... |
| `length()`                       | unlimited                                    | Specifies maximum length of the field. |
| `defaultValue()`                 | null                                         | If new record is created and field value is not set, defaultValue will be used. defaultValue will also be shown in the form if record is not loaded. |
| `visible()`                      | true                                         | Should field be displayed in read-only views? |
| `listData()`, `setValueList()`     | null                                         | Defines possible values of the field. Argument is `array('key'=>'visible description');` Forms and Grids will display "visible description", but "key" is stored as value. |
| `enum()`                         | null                                         | Similar to `listData()` but will use non-associative array as both keys and values. `enum(array('Y','N'));` |

### TABLE_MODEL SPECIFIC META-DATA
| SETTER / GETTER | DEFAULT | EXPLANATION |
|-----------------|---------|-------------|
| from(relation,[actual]) | null | Specifies that the field is imported from joined table. Actual will specify the name of that field in joined table. `addField('address_namo')->from('address','name')`. |
| `searchable()` | false | The field can be used for searching. Used by Filter. |
| `sortable()` | false | The field can be used for sorting. Used by Grid. |

### OBSOLETE FUNCTIONS
| SETTER / GETTER | DEFAULT | EXPLANATION |
|-----------------|---------|-------------|
| `calculated(string/callback)` | null | Obsolete way to define exprerssions. Use `$model->addExpression()`. |
| `refModel('Model_User')` | null | Obsolete way to define references. Use `$model->hasOne('User')`. |