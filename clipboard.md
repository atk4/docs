EXTENDING MODELS

In Agile Toolkit you'll generally be adding functionality by extending the original object rather than changing its code. Say, for example, you have a `user` Model handling your business rules and actions for application users. Then the client decides that they also need a `superuser` entity. A `superuser` does most things the same way as a `user`, does some things differently, and does some new actions not shared with a `user` which use different fields. With most ORMs this wouldn't be easy to achieve without disrupting existing code and tests. With Agile Toolkit, it's simple to extend your Model to add the new requirements:

    TODO: example

Any code using the parent `user` Model will continue to work, because nothing has changed. And there's no danger that new code using the the `superuser` Model will inadvertantly apply `superuser` operations to a mere `user` because you changed the query conditions to select only `superusers` to operate on: there's a clean separation of concerns. Your old `user` tests continue to run, and you add specific tests for the new `superuser` functionality. So changing your code is easy, robust and safe. That's the power of the Extensibility Principle, and you'll find it applied throughout the View and Model objects within the Toolkit.

As an important side effect, this means that Agile Toolkit is radically injectable: any object can be swapped out or reconfigured at any stage with no need for any special planning or configuration. So it's a software tester's dream!


<!--  
-->

