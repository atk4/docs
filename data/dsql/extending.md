# DSQL > Extending The DSQL Class

## Overview

In this section you'll learn how to extend DSQL with sytax for new commands and support for new databases.

In this article we give some tips to help you get oriented. The class is extensively commented, so please read the code if you need more detail.

## Properties Of The DSQL Object

There are a a couple of public properties that are useful when extending the class.

### Field-name quoting

`$q->bt`: by default, DSQL adds a MySQL-style backtick to field-names. Set to an empty string to avoid quoting of field names, or to the quoting character used by your RDBMS.

### Query Templates
                   
Templates are used to construct most common queries. When extending the class, this is where you override default queries and add new query templates.

    public $sql_templates=array(
        'select'=>"select [options] [field] [from] [table] [join] [where] [group] [having] [order] [limit]",
        'insert'=>"insert [options_insert] into [table_noalias] ([set_fields]) values ([set_values])",
        'replace'=>"replace [options_replace] into [table_noalias] ([set_fields]) values ([set_value])",
        'update'=>"update [table_noalias] set [set] [where]",
        'delete'=>"delete from  [table_noalias] [where]",
        'truncate'=>'truncate table [table_noalias]'
    );

## The DSQL Class Structure

To customise the SQL generated for each database, DSQL uses database-specific classes that extend the base class `DB_dsql`:

	// In /atk4/lib/DB/dsql/mysql.php
	class DB_dsql_mysql extends DB_dsql {} 

When connecting to a database, DSQL sets the property `$db->type` based on the supplied connection configuration. With MySQL, for example, the type will be `mysql`. It also sets the `$db->dsql_class` property which determines which class is used.

## Getting DSQL To Use Your Extended Class

To specify your own extended class you can set `'class'=>'myclass'` in the DSN connection configuration array, or manually set the value of `$db->dsql_class` after connection.

Here is a sample extension for MySQL which adds a $q->showTables() command:

	class DB_dsql_mysqlextra extends DB_dsql_mysql {
    	function showTables(){
        	$this->template='show tables';
    	}
	}

In `config.php`, specify your new class:

	$config['dsn']=array(
    	'mysql:host=localhost;dbname=project',
    	'root',
    	'root',
    	'class'='DB_dsql_mysqlextra'
	);

Then in a Model:

	$q=$this->api->db->dsql();

	foreach($q->showTables() as $row){
    	$table=array_pop($row);
    	// ...
	}

## Overriding Existing Methods

If you want to customise `DB_dsql` methods for a specific RDBMS, you can simply override the method in your class. Here's an example from the `postgres` driver:

	class DB_dsql_pgsq extends DB_dsql {

    	// Postgres does not like backticks
    	public $bt='';

    	// Use the extended Postgres syntax for fetching an id on insert
    	function insert(){
        	parent::insert();
        	$this->template.=" returning id";
        	return $this;
    	}
	}

## Adding A sum() Function

As a final example, let's create a method for the `sum()` function:

    function sum($expr, $alias){
        return $this->field($this->expr('sum('. $expr.')'), $alias);
    }
