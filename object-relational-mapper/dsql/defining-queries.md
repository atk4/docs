# DSQL > Defining Queries

## Overview

In this section we cover the methods available in DSQL for defining your query, and in the [next section](/docs/data/dsql/running-queries) we cover the methods for running it. 

We assume a basic knowledge of SQL.

## Expressions & Subqueries

Expressions & Subqueries are used in a number of query definition methods, so we'll cover them first.

### Expressions

The $q->expr() method is used to access the full range of functions within your RDBMS. Simply pass in the expression as a string:

    $q->expr('sum(bonus)')

You can use named parameters in expressions to ensure that untrusted data is safely quoted:

	$q->where('age', $q->expr('between [left] and [right]', array('left'=>$l, 'right'=>$r)));

The expression will be parsed as a template, which is why we use the the `[placeholder]` syntax from the Agile Toolkit template engine.

### Subqueries

You can use $q->dsql() to produce sub-queries. Calling this method will create a new DSQL object, which is used in a similar way to expressions. Or an existing query object can used, if appropriate.

  	// Outputs: 'select name from author where book_id in (select id from book where is_rented=:a)'    array('a'=>'Y')
	$q->table('author')
 		->field('name')
 		->where('book_id', $q->dsql()->table('book')->where('is_rented', 'Y'));

## Tables

Use the table() method to set one or more tables for your query:

	table('user') 								// Sets the main table
	table('user', 'u') 							// Aliases the table with 'u'

	table('user')->table('salary') 				// Sets multiple tables (for joins, etc)
	table('user', 'u')->table('salary', 's') 	// Sets multiple aliased tables
	
	table(array('user', 'salary')) 				// Alternative syntax for setting multiple tables
	table(array('u'=>'user','s'=>'salary')); 	// Alternative syntax for multiple aliased tables

## Fields

Use the field() method to add physical ﬁelds to the query, or ﬁelds expressed with a formula (expressions). You can also set field aliases to shorten long field names for debugging or logging.

If you don't call ﬁeld() in your query, DSQL will default to '*' and select all fields.

	ﬁeld('name') 									// Set a single field
	ﬁeld('name', 'user') 							// Set field 'name' in explicit table 'user' (for joins, etc)

	ﬁeld('first_name, last_name'); 					// Set multiple fields
	ﬁeld('name', 'user')->ﬁeld('line1', 'address')	// Set multiple fields

	ﬁeld(array('name', 'surname')); 				// Alternative syntax for multiple fields

	ﬁeld(array('n'=>'name','s'=>'surname'));  		// Set aliases for ﬁelds
	ﬁeld(array('n'=>'name'), 'user'); 				// Set aliased field with explicit table

	// Set a field to hold the result of an expression
	ﬁeld($q->expr('year(now)-year(birth)', age'); 			
	
	// Set a field to hold the result of a subquery
	ﬁeld($q->dsql()->table('book')->ﬁeld('count(*)'), 'num_books'); 

## Conditions (WHERE Clauses)

Calling the $query->where() method will filter the dataset returned by SELECT queries, and the rows affected by UPDATE and DELETE queries.

### Basic usage

To use standard WHERE clause operators such as =, >, <>, LIKE, NOT, IN or BETWEEN, or to use vendor-specific operators:

	$q->where('id', '=', $num);       				// where id=:a    		'a'=>$num
	$q->where('id', $num);       					// Shorthand for the query above

	$q->where('id', '>', $num);       				// where id>:a    		'a'=>$num
	$q->where('id', '<>', $num);      				// where id<>:a   		'a'=>num

	$q->where('id', 'like', $str); 					// where id like :a   	'a'=>$str
	$q->where('id', 'not [id]', array('id'=>$id)) 	// where id not :a   	'a'=>$id
	$q->where('id', 'in', array($num, $num2)); 		// where id in(:a,:b)   'a'=>$num, 'b'=>$num2
	$q->where('id', 'between', array($num, $num2));	// where id between(:a,:b)   'a'=>$num, 'b'=>$num2

If the second argument is 'null' then the operation 'is NULL' is used automatically:

	$q->where('id', null);       		// where id is NULL
	$q->where('id', 'is', null);   		// where id is NULL
	$q->where('id', '!=', null);   		// where id is NOT NULL
	$q->where('id', 'is not', null);	// where id is NOT NULL

### Comparing field values

To compare field values, use the getField() helper method to format a quoted field name:

	$q->where('income',  '>', $q->getField('expense'));
	$q->where($q->getField('income'), '>', $q->getField('expense'));

### 'IN' clauses:
	
	// Outputs: 'where state in (open, closed, resolved)'
	$q->where('state', array('open', 'closed', 'resolved'));  
	
### Expressions: expr()

	// Single argument mode
	$q->where($q->expr('a=b'));

	// Using expressions with operators
	$q->where('date', '>', $q->expr('DATE_SUB(CURDATE(), INTERVAL 2 MONTH)');

	// You can use expressions on both sides of the equation
	$q->where($this->expr('length(password)'), $q->expr('between 3 and 10'));

### Subqueries

	// Display the names of authors who have more than 5 books
  	// Outputs: select name from author where (select count(*) from book where author_id=author.id)>5

	$q->table('author')
  		->field('name')
  		->where(
    		$q->dsql()
      			->table('book')
      			->where('author_id', $q->getField('id'))
      			->field('count(*)'), '>',5);    


### AND Conditions: where()->where()

Calling where() multiple times will require all of the conditions to be met, using the AND operator:

	// where email is NULL and phone is NULL
	$q->where('email', null)->where('phone', null);	

### OR Conditions: where(array)

Calling where() with a single array argument will use OR to join those conditions.

	// Generates: where (id=:a or id=:b)  array('a'=>1, 'b'=>2)

	$q->where(array(
  		array('id',1),
  		array('id',2)
  	));


You can even specify arrays recursively:

	// Generates: where (len(name)>:a or a=b)  array('a'=>5)

	$q->where(array(
  		array($q->expr('len(name)'),'>',5),
  		array($q->expr('a=b')) 
  	));

	Or you might prefer the alternative syntax for 'OR' conditions:

	$q->where( $q->or()->where('a', 1)->where('b', '>', 5) );

The second format here will also use a table preﬁx for expense, which is nice 
when you are using multiple joins. You can also use `getField()` for the ﬁrst argument:

	where($q->getField('income'),'>', $q->getField('expense'));

## HAVING clauses

The having() method has the same syntax as the where() method described above.

## Joins

Use the `join($table_name [, $field_name] [, $join_type])` method to query data from multiple tables.

### Default Conventions

If you don't specify field names explictly, `join()` assumes:

* Primary keys are named 'id' (eg, `author.id`)
* Foreign keys are named 'tablename\_id' (eg, `book.author_id`)

### Setting Inner Joins

To set a standard INNER JOIN:

	// Join 'author' on 'author.id=book.author_id'
	$q->table('book')->join('author');

	// Specify a non-default primary key in 'author'
	// Joins 'author' on 'author.author_id=book.author_id'
	$q->table('book')->join('author.author_id');

	// Specify a non-default foreign key in 'book'
	// Joins 'author' on 'author.author.id=book.author_code'
	$q->table('book')->join('author', 'book.author_code');

	// Join info on 'info.issn=book.issn or info.is_active=1'
	join('info',$q->orExpr()->where('info.issn',$q->getField('issn')->where('info.is_active',1))

### Setting other join types

Use the third argument in join() to specify a non-default join type. You can use 'left', 'right', 'outer' or any type supported by your RDBMS.

	// Joins with 'left join manager on manager.id=user.manager_id'
	$q->table('user')->join('manager', null, 'left');
	
### Setting a table alias

Similarly to field() you can use array('alias'=>'table') as a first argument to the join()

	// Outputs 'join manager m on m.id=user.manager_id'
	$q->table('user')->join(array('m'=>'manager'));

	// Set multiple aliases
	$q->table('user')->join(array('m'=>'manager','a'=>'address'));
	$q->table('book)->join(array('a'=>'author','c'=>'chapter.book_id')); // two joins

DSQL builds your joins for you, so table aliases are only required:

* When you are joining to a table multiple times
* When a table joins with itself
* When a subquery joins to the same table as a parent query.

### Using expressions in joins

If  you need a further ﬂexibility with the join, you can use expressions. You can chain join() expressions using andExpr() and orExpr().

	 // Join info on 'info.issn=book.issn and info.is_active=1'
	join('info',$q->andExpr()->where('info.issn', $q->getField('issn')->where('info.is_active',1))

	 // Join info on 'info.issn=book.issn or info.is_active=1'
	join('info',$q->orExpr()->where('info.issn',$q->getField('issn')->where('info.is_active',1))

## Grouping

Use the `group()` method to set GROUP BY clauses.

	group('sex') 						// Group by a single field
	group('sex, age');					// Group by multiple fields		
	group(array('sex', 'age'));			// Alternative syntax for multiple fields
	
	// Group by expression
	group($q->expr('year(created)'));

	// Group by field and expression
	group(array('sex', $q->expr('year(created)')));

## Ordering

Use the `order()` method to order SELECT results by one or more fields or expressions. 

The default direction is ascending, but you can specify 'asc' if desired for clarity. Use 'desc' to set descending ordering.

	order('age') 						// Ascending order by field 'age'
	order('age asc') 					// Set ascending order explicitly
	order('age desc'); 					// Descending order by field 'age'

	order('age, sex'); 					// Ascending order by fields 'age' & 'sex'
	order(array('age', 'sex')); 		// Alternative syntax for multiple fields

	order('age desc, sex');				// Order by 'age' (descending) & 'sex' (ascending)				
	order(array('age desc', 'sex'));	// Alternative syntax 

	order($q->expr('year(birth)'));				// Ascending by expression
	order($q->expr('year(birth)', true));		// Descending by expression

## Limiting

Use the `limit()` method to limit the result set:

	limit($num_rows)
	limit($num_rows, $offset)

## Options

Many SQL commands offer options such as SELECT DISTINCT or INSERT DELAYED. There are currently two methods for setting options:

	option('distinct'); 				// Set options for SELECT queries
	option('distinct high_priority'); 	// Set multiple options

	option_insert('delayed'); 			// Set options for INSERT queries

## Setting Values

Use the `set()` method to set values in INSERT and UPDATE queries:
	
	set('id', $_GET['id'])); 			// Unstrusted data
	set('votes', $q->expr('votes+1'));	// Expression

	// Setting multiple values
	set(array('votes' => $q->expr('votes+1'), 'poll'=>$poll ));

## Deleting Arguments

The `del()` method will unset arguments from your DSQL query object:

	$q->del('ﬁelds');

The `reset()` method will delete all arguments

A common use is to change ﬁelds on an arbitrary query:

	$r=clone $q;
	$count = $r->del('fields')->field('count(*)')->getOne();

## Calling Built-In Functions

As an alternative to `expr()`, you can use the `fx()` method for calling your DB's built-in functions:

	// Outputs 'call myfunc($arg1, if($x1, $x2, $x3))'
	$q->dsql()->fx('if', array($x1, $x2, $x3))

## Calling Custom Functions & Stored Procedures

When you call your own user-defined functions in SQL, you typically use the syntax:

	call myfunc(args)

To replicate this in DSQL:

	$q->call('myfunc', array($arg1, $arg2));

# Some Portable Methods
 
As an alternative to `expr()`, Agile Toolkit currently offers the following cross-database methods:

	concat(arg, arg,...) 	// string concatination
	describe(table) 		// select table schema
	random() 				// return random value 0..1
	sum(ﬁeld) 				// returns result of expression sum(..)
	count(ﬁeld) 			// returns result of expression for count(..). Argument is '*' if unspeciﬁed.
