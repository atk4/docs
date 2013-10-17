Application Class Features > Multiple Interfaces
 ----

Most web software use multiple web interfaces. User Frontend and Admin are separate Web interfaces, both of which take advantage of the page concept. While backend functionality is shared between both - Models, Controllers, only Administrators may access the Admin interface so the "Auth" class would be configured differently.

#### Multi-lingual or satellite sites

Sometimes you will want your software to run in multiple regions mostly unchanged, however you need to have the flexibility to add regional or site-specific changes. In this case you will want to create an intermediate API (parent), which is then extended for each regional site.

Site1 and Site2 API classes can change theme, skin or add additional templates/translations for localization, but would rely on the same common Application class for all the base functionality.

## Using Multiple Sub-Applications

Applications often require multiple sub-sites with their own authentication and sessions. And they may also require a CRON API for running time-related scripts. 

This is easily achieved with APIs. APIs will run their own authentication and sessions entirely independently of each other.

As we've seen in the section on Project Organization, you can set up multiple apps within your project using symbolic links to share resources simply.

#### Realms

Realms are required once you run more than one app in your project. 

A Realm is essentially a namespace mechanism, ensuring that the different parts of your app will run independently. You pass the Realm name to the API class when you create the API object:

    $api = new Store('Store');

By convention, you would give your API class the same name as its Realm.

All objects also store reference to the Application class through `$this->api` property.

It's even possible to create an Application from inside another one. The new app will function perfectly in its own independent namespace. This can be useful for === TODO: Use Case?? ===

