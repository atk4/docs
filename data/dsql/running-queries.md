# DSQL > Running Queries

## Overview

When your run your query, DSQL parses your query parameters into the following templates:

	'select'=>"select [options] [field] [from] [table] [join] [where] [group] [having] [order] [limit]"

	'insert'=>"insert [options\_insert] into [table\_noalias] ([set\_fields]) values ([set\_values])"

	'update'=>"update [table\_noalias] set [set] [where]"

	'replace'=>"replace [options\_replace] into [table\_noalias] ([set\_fields]) value ([set\_value])"

	'delete'=>"delete from  [table\_noalias] [where]"

	'truncate'=>'truncate table [table\_noalias]'

We'll cover each of these queries in turn.

## Select

### Getting your data in a single call

Use one of these useful helper methods:

	// Returns all rows as an array of hashes, or array() if no results.
	$data = $q->getAll();     
	
	// Shorthand alias for getAll();
	$data = $q->get();        
	
	// Returns a non-associative array of the first row of data, or 'null' if no results.
	$data = $q->getRow();     

	// Returns a hash of the first row of data, or 'null' if no results.
	$data = $q->getHash();     

	// Returns the value of the first field in the first row of data, or 'null' if no results or the result was 'null'.
	$data = $q->getOne();     

Calling getRow() multiple times will continue to return the first row of data.

### Getting your data iteratively

These methods execute the query once, then enable you to loop through the result set. They return `null` if no more results are returned from the database.

The `fetch()` method enables you to loop through results fetching one row at a time: 

	while($row = $q->fetch()){
  		$my_value = $row['my_fieldname'];
	}

The DSQL object implements the Iterator interface, which means you can use it inside a foreach() block. The results are identical to the `fetch()` method, but the syntax is nicer:

	foreach($query as $row){
  		$my_value = $row['my_fieldname'];
	}

## Insert

	$this->dsql()->table('user')
		->set('username', 'Foo')
		->insert(); 

## Update
	
	$this->dsql()->table('user')
		->set('username', 'Foo')
		->where('id', 123)
		->update(); 

## Replace

<!-- Please check - not familiar with REPLACE -->

This is a template for the MySQL REPLACE INTO query:

	$this->dsql()->table('post')
		->set(array('id'=>123, 'username'=>'Foo'))
		->replace();

## Delete

	$this->dsql()->table('post')
		->where('id', '<', 123)
		->delete();

## Truncate

Used to empty a table of data:

	$this->dsql()->table('user')->truncate();

## Using preexec()
 
Because DSQL is lazy, a query is not normally executed till you start iterating through the result set. If required, you can run the query earlier using the `preexec()` method.

## Counting The Rows In The Result Set

The most common use-case for `preexec()` is determining the number of rows in the result set before iterating though the results. 

Method `foundRows()` executes a SELECT query and returns the number of rows in the result set. Method `calcFoundRows()` provides a hint to the RDBMS (where supported) to improve query performance.

	// Execute a configured query and count the rows returned
	$num_rows = $q->calcFoundRows()->preexec()->foundRows();

	foreach($q as $data){
 		// ..
	}