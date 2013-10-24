Core > NAMESPACES
----

Agile Toolkit uses namespaces for Add-on management. Here is how you define a class inside a add-on and call it:

    // inside atk4-addons/myaddon/Test.php
    namespace myaddon;
    class Test extends \View {    // backslash is not typo. It refers to a top-level namespace
        function init(){
            parent::init();
            $this->set('Hello from Add-on');
        }
    }

    // Using class from add-on
    $this->add('myaddon/Test');

To learn more about developing add-ons, please read [Add-on developer guide](developing-addons.md "Add-on developer guide").