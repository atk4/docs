# Agile ORM > Overview

== VERY ROUGH - WILL RETURN TO THIS WHEN I HAVE A BETTER HANDLE ON THE ORM ==

## What Is Agile ORM?

Agile ORM is an [Object-Relational Mapper](http://en.wikipedia.org/wiki/Object-relational_mapping). It enables you to build Models with the lightweight ease of [Active Record](http://en.wikipedia.org/wiki/Active_record) and much of flexibility of heavyweight [Data Mapper](http://en.wikipedia.org/wiki/Data_mapper_pattern) ORMs such as [Doctrine](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/).

## Design Goals

### The full expressiveness of SQL

Based on [DSQL](/docs/data/dsql/overview), Agile ORM models offer an object-oriented alternative to SQL that enables you to access the full power of your RDBMS.

### Abstraction from the data structure

Some ORMs map rather directly from the physical structure of your tables to the Models they generate. With Agile ORM, your Models are well abstracted from the underlying data design - you can adapt your data schema without having to change your Model structure.

### Reusable model components

The flexible design of DSQL means that it's easy to alter all aspects of your queries, making your Models more extensible.

The ability to extend and adapt Models is a powerful strategy. It allows you to leave your existing code intact, but add a new Model for new use-cases. 

For example a `User` Model for a `user` table might be extended into an  `Active_User` Model with operations that only affect active users, and a `Moderator` Model which can run a wider range of operations against a wider range of fields.

The ability to extend your models helps you greatly reduce the testing required after a structural change to your database.

### Built-in security

Because parameterization of untrusted data is handled conveniently and automatically, the danger of SQL injection exploits is greatly reduced.

### Testability

Unlike some Active Record type implementations, Agile ORM offers full inversion of control and makes it easy to alter data sources and settings at runtime.

### No PHP code generation

Some frameworks handle database interaction though code generation, but in our view this creates bloat and inflexibility. Through its close integration with View components Agile ORM matches the productivity of code generators but is much more adaptable.

### No features for managing your database structure

By design, Agile ORM won't try to create or alter your database structure:

* **Security**: allowing your application to change your database structure can open up security issues &ndash; this is a job that's handled more securely by dedicated migration scripts.

* **Migration flexibility**: rather than expecting you to learn a new syntax to handle schema upgrades, Agile Toolkit works directly with SQL scripts. This offers the full feature-set of SQL to alter your database structure and manage your data, and enables you to work with the many powerful GUI tools designed for the purpose.


### No significant performance overhead

With many ORMs you'll experience a significant performance hit over hand-crafted SQL. Not with Agile ORM: we use an innovative approach to deliver all the benefits of an ORM without the overhead.
