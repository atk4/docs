[Home](../readme.md "Home")


In Agile Toolkit Views can interact with the Data Layer through Models. Model is a class implementing [Active Record](http://en.wikipedia.org/wiki/Active_record_pattern) and [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping) as well as holding meta-data for the objects. 

One Model instance 


![Data Model](orm/orm-integration.jpg "Data Model")

You must understand that there are two types of models - Relational and Non-Relational (or NoSQL) and that Model_Table (relational) extends the other. To most of the User Interface both classes are compatible, however both model implementation use a very different way to interact with data.

Before we can understand Relational Models, we must first look into the implementation of the DSQL layer. Models are described in the further chapters.

### Connecting to the database

To connect to the database you need to call `api->dbConnect();` For this method to work without any arguments, you need to specify DSN (connection info) in config.php file. Alternatively you can also specify DSN as an argument, although be advised that if you use passwords inside GET argument, they will be visible on exception screen, when your database is unreachable. You can disable error output on the screen, see chapter for "Application" for more information.

* Specifying DNS
* Using different connection modes
* Counting number of queries in application
* Using multiple connections
* Connecting to noSQL databases

[Next Section](section1.md "Next Section")