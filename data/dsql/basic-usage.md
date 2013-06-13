# DSQL > Basic Usage

## Workflow

Let's walk through a basic query before we get down to details. To use DSQL:

1. Connect to the database
1. Create your DSQL query object
1. Configure the query
1. Execute the query.

Finally, if you experience problems you can debug the query.

<!-- TODO: pull the connection section out under its own heading so people can find it! -->

## Connecting To The Database

You set up your default database connection in your config file under `$config['dsn']`.

Agile Toolkit offers two formats for your connection configurations.

### DSN-style connection strings

There is a simple connection string based on the old PHP DSN library:

	// Syntax
	database://username:password@host/database

	// Example for MySQL
	$config['dsn']='mysql://user:secret@localhost/my_db';

The database definition strings include: `mysql`, `sqlite` and `pgsql`.

### PDO-style connection strings
	
If your needs are more complex, you can use the PDO-style Data Source Name (DSN) connection strings for [MySQL](http://php.net/manual/en/ref.pdo-mysql.connection.php), [SQLite](http://www.php.net/manual/en/ref.pdo-sqlite.connection.php), [Postgres](http://www.php.net/manual/en/ref.pdo-pgsql.connection.php), [Oracle](http://www.php.net/manual/en/ref.pdo-oci.connection.php) and the other databases supported by PDO.

### Connecting to the default database

You will normally connect to the database by calling `$api->dbConnect()` in the `init()` method of your `Frontend` class. `dbConnect()`  automatically reads the default database settings from your configuration file (`$config['dsn']`) and initializes a connection. 

    function init(){
        parent::init();
        $this->dbConnect();
    }

Once created, the connection object is accessible through the $api->db property.

### Connecting to additional databases

To add additional connections, create more instances of the `DB` class:

	$mydb = $this->add('DB')->connect('mysql://user:pass@localhost/testdb');

But it's usually more convenient and secure to store your configuration settings in your configuration file:

	$config['my_dsn'] = "mysql://user:password@localhost/testdb";

Then in your application you call:

	$mydb=$this->add('DB')->connect('my_dsn');

If you call connect() without an argument it will look for a DSN string in $config['dsn'].

Database connections in Agile Toolkit are lazy &ndash; they will not be physically created until you execute a query.

## Creating The DSQL Query Object

DSQL query objects are created by calling the dsql() method of the default DB object or any additional DB objects. This function returns an empty query object which you use to build your query.

	// Use the default connection
	$query = $this->api->db->dsql();

	// Use additional connections
	$query = $mydb->dsql();

Finally, you can also clone a `$dsql` object which will give you an  exact copy. There are other ways to generate a new DSQL object, for example calling $model->dsql() will return a DSQL query object initialized for your $model. This query would have 'table', and 'where' clauses set and might also use some joins.

You can also call $model->dsql() which will return a DSQL query object initialized with your existing Model settings, which can then be customized.

And you can clone a `$dsql` query object, which will give you an exact copy.

When you create a DSQL object using an existing connection it may optimise the SQL syntax for the database being used, but if you create a DSQL object before connecting to the database it will generate generic SQL. So you will normally want to set up a connection before creating a query object.

## Configuring The Query

DSQL offers a [comprehensive range of methods](/docs/data/dsql/defining-queries) for configuring your query. You can call these methods several times and in any order &ndash; you can even adapt and reuse a query after it has been executed. The methods can be chained:

	// Set up a query object
	$q = $this->api->db->dsql();
	
	// Set your initial query configuration
	$q->table('user')
  		->where('type','admin')
  		->field('id'); 					// Return the 'id' field
  
	// Refine the configuration & execute the query
	$data = $q->order('created_time')  	// Order the result set
  		->field('name, surname')  		// Return additional fields
  		->getAll(); 					// Run the query

	// Produces: 
	//   $data=array(
	//      array('id'=>1, 'name'=>'John', 'surname'=>'Smith'),
	//      array('id'=>2, 'name'=>'Joline', 'surname'=>'Bloggs')
	//    ); 

When you build the query by calling methods, your arguments could be:

1. Identifiers, such as `id`
2. Parameters, such as `$_GET['id']`
3. Expressions, such as `now()`

Always call DSQL methods with the correct type of argument, so they are quoted correctly in the SQL output.

	$q=$this->api->db->dsql()->table('user'); 
	$q->where('id', $_GET['id']);
	$q->where('expired', '<', $q->expr('now()'));
	$data = $q->field('count(*)')->getOne();

For MySQL the example above will produce the query:

    select count(*) from `id`=123 and `expired` < now();

Calling $query->table('my_table') is the only requirement before you execute your query.

## Executing The Query

DSQL offers [a number of methods](/docs/data/dsql/running-queries) for executing your query and fetching the result set. For example:

	$data = $q->getAll(); 
	$data = $q->getOne();

## Debugging The Query

DSQL has a method debug() which will echo your query as it's being executed:

	$q=$this->api->db->dsql();
	$q->table('user');
	$q->field('name');
	$q->debug();
	$data = $q->getAll();    // Will output debugging information

You can also call `getDebugQuery()` which return your query as nicely formatted HTML.
