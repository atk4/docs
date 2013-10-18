///////////////////////////////////
// SCRAPBOOK FOR REFACTORING
///////////////////////////////////

#### Rendering

Agile Toolkit provides support for partial rendering of views based on 3 criteria: cut_page - outputs only page's content (used by dialogs and frames), cut_object - outputs content of a certain object (used when object is reloaded) and cut_region - outputs content of a certain region in template. (cut_region is not safe and should be avoided). 

#### Layouts

A Layout is a region in the shared template (shared.html), which is parsed by the API class. If the region is defined, then a corresponding layout function is called.

The first and most significant layout element is "Content". Content will determine which page is requested and proceed with all the logic of page initialization. However if "Content" is not present in the API template (`shared.html`), the page will not be initialized.

You can add additional layout elements. For example, calling addLayout('Menu') inside api->init() which will instruct API to call layout_Menu() method if tag &lt;?Menu?&gt; is found when parsing the template.

This approach is designed to have multiple layout components initialized based on the use of global template. This way your application can easily produce output for multiple form-factor devices. When opened on a mobile device, your application can load a different application template (such as `shared-mobile.html`) which would omit some tags and bypass initialization of certain regions.

You can specify which template file is used by specifying it from inside the defaultTemplate() function of your API.

    function defaultTemplate(){
        if($_GET['mobile']){
            return array('shared-mobile');
        }else{
            return array('shared');
        }
    }
