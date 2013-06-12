# DSQL > Overview

## What Is DSQL?

DSQL ('Dynamic SQL') is a Query Builder for dynamic SQL. A DSQL object represents a single query and collects information about fields, conditions, expressions and joins for rendering into SQL. It then enables you to run the query and iterate through any results.

With DSQL your Models can build queries of open-ended complexity conveniently, flexibly and securely.

## Why Use A Query Builder?

Agile Toolkit executes all of its queries through DSQL. To SQL veterans this may seem limiting, but for an extensible framework DSQL offers essential benefits:
	
* Query-related information can be passed between objects
* DSQL query objects can be modified much more easily and safely than SQL strings
* DSQL can optimise the SQL generated for each supported RDBMS
* You greatly reduce the danger of SQL injection exploits.

## Features

### Write portable SQL, or access the full power of your RDBMS

DSQL is built on PHP's [PDO database abstraction library](http://php.net/manual/en/book.pdo.php). 

It's been tested with MySQL, SQLite, PostgreSQL and Oracle, but should work with other ANSI-SQL compatible databases supported by PDO.

If you need to write portable code, you have access to PDO's database abstraction features. 

Or if you prefer, DSQL will also give you access to most of the vendor-specific features of your chosen RDBMS. It's easy to [extend DSQL](/docs/data/dsql/extending) if it lacks any specific feature you require. And if you need to do something very specific with your database you can access PDO statement objects directly through `$dsql->stmt`. 
	
### Modify existing queries at any time

Unlike most query builders DSQL allows you to add or remove any element of your query at any time, including fields, joins, conditions and parametric variables. You can even change the query type &ndash; from SELECT to DELETE for example. After you execute the query you can still reconfigure and reuse the object.

This is important when you need to pass query information between Models or even allow a Controller or Addon to interact with a query. Queries can be cloned, adapted and extended. And multiple queries can coexist and execute without affecting each other.

### Use a DSQL object as a subquery

Another cool feature is the ability to use existing query objects within a DSQL query while dynamically preserving the existing object's parametric variables. This is typically used for subqueries.

### Greatly reduce the danger of SQL injection

While all modern ORMS and Query Builders offer a mechanism for parametizing untrusted input it's often rather tedious to use.

    // PDO Example
	
	$sth = $dbh->prepare("select name from user where id = :id"); 	
    $sth->execute(array(':id'=>$id));	
	$name = $sth->fetchColumn();

In DSQL the syntax is simpler, so even a harassed developer is less likely to take shortcuts and write unsafe code.

    // DSQL Example

	$name = $dsql->table('user')->field('name')->where('id', $id)->getOne();

Though it's important to remember that you may still be vulnerable to vendor-specific buffer overflow and stored procedure exploits. So it's always prudent to validate untrusted data, and to cast your arguments to the correct type:

	$name = $dsql->table('user')->field('name')->where('id', (int)$_GET['id'])->getOne();

### Enjoy the benefits without performance issues

DSQL is lean and fast. It adds a trivial overhead when building the query string, but creates no overheads when executing the query and fetching data. You will rarely, if ever, encounter a use-case where you need to use raw SQL for performance reasons.

## Code Example

Here's a simple example of DSQL in action:

	foreach(
		$this->api->db->dsql()
			->table('user')
			->where('gender','M')
			as $row) {
				echo "Name: " . $row['name'];."\n";
			}

## How It Works

Unlike most Query Builders, the DSQL object stores your query properties and only renders SQL when the query is run, using query templates such as:

  'select'=>"select [options] [field] [from] [table] [join] [where] [group] [having] [order] [limit]"

There are several pre-defined templates and you can easily add your own. This is how DSQL achieves its flexibility.

## Uses

In Agile Toolkit, you build your relational Models with the [Agile ORM](/docs/data/agile-orm/overview). DSQL is primarily a low-level layer used by the ORM to generate SQL using its own convenient and object-oriented query syntax. Agile Models will cover most of your query needs, and whenever possible you should use this Model syntax rather than accessing DSQL directly. 

	// BAD code:

	$name = $dsql->table('user')->field('name')->where('id',(int)$_GET['id'])->getOne();

	// GOOD Code:

	$name = $this->add('Model_User')->loadBy((int)$_GET['id'])->get('name');

In both cases the result is one query, so there is almost no performance benefit to using DSQL.

You only use DSQL directly for all or part of a query when:

* You require SQL features that can't be accessed through the Model query syntax, such as expressions
* You are extending the ORM Model classes to add new features.
