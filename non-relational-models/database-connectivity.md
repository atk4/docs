# Database connection  

Agile Toolkit relies on PHP/PDO to access database. As a result, multiple data-base vendors are supported, however they may vary in slight details. If your database is not supported, consider writing a simple driver, which will help Agile Toolkit generate certain types of queries. Luckily you already have drivers for MySQL, PostgreSQL and SQLite to look for. If you do develop your own driver, consider contributing it back to Agile Toolkit or make it available through an add-on.

## Configuring access
The definition of the format of an SQL connection string goes back to the old days of year 2000 and is based on the PEAR::DB format of DSN. Here is a typical connection string - which is normally placed inside your config.php:

    $config['dsn']='mysql://user:password@localhost/dbname';

    // define port:
    // $config['dsn']='mysql://user:password:8888@localhost/dbname';

    // through socket
    // $config['dsn']='mysql://user:password:/tmp/sock@localhost/dbname';

The DSN format is converted into a PDO:DSN array. If you wish to bypass the PEAR-compatible format, you can specify PDO DSN right away in an array

    $config['dsn']=array('mysql','user','password', $options);

## Establishing a Connection
The actual connection is established by Agile Toolkit through this line:

    $this->api->dbConnect();

Agile Toolkit uses persistant connections, but if you wish to bypass connection alltogether, you can strategically place this line inside a controller or only on some of the pages which require database access.

If you wish to create additional connections, you can create 'DB' object directly like this:

    $db = $this->add('DB')->connect($dsn);

### SQL Queries Access from Agile Toolkit
Agile Toolkit was one of the first web frameworks to implement dynamic queries in 2003. DSQL is a very important component of Agile Toolkit, it is a fundamental concept behind Relational Models. The rest of the Views rely on Models to perform data operations.

DSQL is a query builder and allows developer to construct query of any complexity through method calls. All of the methods are designed to be SQL-injection safe and will rely on parametric arguments of PDO. DSQL will also attempt to cache some of the query statements, which is extremely useful if only arguments are being modified

Agile Toolkit does not have a single query-string in it's traditional sense: "SELECT blah FROM tbl". Your job as a good programmer is not to introduce them.

    $q = $this->api->db->dsql();
    $q->table('employee');
    $q->field('salary');
    $q->where('id',1);
    echo $q." = ".$q->getOne();

Queries can also be iterated through, but it will be explained later.

Debugging Database Queries
To make any DSQL object expose its queries, execute the debug() method:

    $q->debug();

This will output the queries to the screen as they are being parsed. Even through the parametric values are used, the debug query will place the actual values into the query enabling you to copy-paste output into your SQL console.